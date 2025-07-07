# Safe-AI-Therapist-Chatbot-simple
Using gemini api, and gemini blocks to prevent and flagging extreme cases

## Overview (logic flow):
### This is a very simple project, straightforward, with only 3 files.
- The first file is TherapyPage.tsx: this has a simple chat interface to chat with our ai, send message via fetch
- The second file is route.ts: handle the interraction with ai api, it fetch the users'messages, hold current chat history, and flag if user is facing crisis or too extreme ( like might have suicidal thoughts)
- When user is too extreme or facing crisis, the AI raise a flag to run the components/Helpline.tsx which will display in the chat window to prompt the user to seek real help
- And google ai is extreme helpful, why? You may read DETAILS.md to understand or go to this page <a>https://ai.google.dev/gemini-api/docs/safety-settings#javascript</a>

## READ DETAILS.md (read - optional, but highly recommended)

## Project Setup
### AI SDK (by Vercel)
<br/>
The AI SDK is the TypeScript toolkit designed to help developers build AI-powered applications and agents with React, Next.js, Vue, Svelte, Node.js

*we will be using react.js vite for typescript, tailwindcss in this project*
```bash
npm create vite@latest
```
*then, do tailwind css*
<a>https://tailwindcss.com/docs/installation/using-vite</a>

*then, install this package*
```bash
npm i ai # for ai sdk
npm install lucide-react # for icons
npm i remark-gfm
npm i react-markdown # for bold characters
```
then do this, but you can visit ai-sdk.dev to see more, but we will be working with gemini api in this project
```bash
npm install @ai-sdk/google
# or
pnpm add @ai-sdk/google
# or
yarn add @ai-sdk/google
```

## create a file: .env.local
create an env file
```bash
GOOGLE_GENERATIVE_AI_API_KEY="YOUR_GEMINI_API_KEY"
```

## CODE:
### TherapistPage.tsx
```bash
// 'use client';

import { useEffect, useRef, useState } from 'react';
import Helpline from '@/components/Helpline';
import { HeartHandshake, Send } from 'lucide-react';
import Markdown from 'react-markdown';
import remarkGfm from 'remark-gfm';

type ChatMessage = {
  role: 'user' | 'assistant';
  content: string[];
};

export default function Page() {
  const [messages, setMessages] = useState<ChatMessage[]>([]);
  const [input, setInput] = useState('');
  const [crisis, setCrisis] = useState(false);
  const [isTyping, setIsTyping] = useState(false);
  const bottomRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    bottomRef.current?.scrollIntoView({ behavior: 'smooth' });
  }, [messages, isTyping]);

  async function sendMessage(e: React.FormEvent) {
    e.preventDefault();
    if (!input.trim()) return;

    const userMsg: ChatMessage = {
      role: 'user',
      content: [input],
    };
    setMessages((prev) => [...prev, userMsg]);
    setInput('');
    setIsTyping(true);

    try {
      const res = await fetch('/api/chat', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          message: input,
          history: messages,
        }),
      });

      if (!res.ok) throw new Error('API Error');

      const data = await res.json();
      const { response, isCrisis } = data;

      const aiMsg: ChatMessage = {
        role: 'assistant',
        content: response,
      };

      if (isCrisis) setCrisis(true);
      setMessages((prev) => [...prev, aiMsg]);
    } catch (err) {
      console.error(err);
      setMessages((prev) => [
        ...prev,
        { role: 'assistant', content: ['Sorry, something went wrong.'] },
      ]);
    } finally {
      setIsTyping(false);
    }
  }

  return (
    <main className="flex flex-col h-screen max-w-2xl mx-auto px-4 py-6">
      <h1 className="text-2xl font-bold mb-4 flex items-center">
        <HeartHandshake className="mr-1" />
        <span>AI Therapist</span>
      </h1>

      <div className="flex-1 overflow-y-auto space-y-2 bg-white p-4 rounded-lg">
        {messages.map((msg, idx) => (
          <div
            key={idx}
            className={`flex flex-col ${msg.role === 'user' ? 'items-end' : 'items-start'}`}
          >
            {msg.content.map((text, i) => (
              <div
                key={`${idx}-${i}`}
                className={`max-w-xs p-3 rounded-lg shadow mb-2 ${
                  msg.role === 'user'
                    ? 'bg-blue-500 text-white rounded-br-none'
                    : 'bg-gray-200 text-black rounded-bl-none'
                }`}
              >
                <div className="prose prose-sm max-w-full break-words">
                  <Markdown remarkPlugins={[remarkGfm]}>{text}</Markdown>
                </div>
              </div>
            ))}
          </div>
        ))}

        {isTyping && (
          <div className="flex justify-start">
            <div className="max-w-xs p-3 rounded-lg shadow bg-white text-gray-400 italic rounded-bl-none">
              AI is typing...
            </div>
          </div>
        )}

        {crisis && <Helpline />}
        <div ref={bottomRef} />
      </div>

      <form
        onSubmit={sendMessage}
        className="flex mt-4 gap-2 border-2 border-black p-2 rounded-xl"
      >
        <input
          type="text"
          className="flex-1 border border-white rounded-lg focus:outline-none focus:ring-2 focus:ring-white"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          placeholder="Type your thoughts..."
        />
        <button
          type="submit"
          className="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg shadow"
        >
          <Send className="w-7 h-7" />
        </button>
      </form>
    </main>
  );
}
```
### /api/chat/route.ts
```bash
import { google } from '@ai-sdk/google';
import { generateText } from 'ai';
import { NextRequest, NextResponse } from 'next/server';
import type { CoreMessage } from 'ai';

export async function POST(req: NextRequest) {
  try {
    const { message, history } = await req.json();

    const messages: CoreMessage[] = [
      {
        role: 'system',
        content: `You are a compassionate AI therapist.

Respond in **well-formatted Markdown** with a warm, human tone.

Use:
- Markdown (**bold**, _italic_, ~~strikethrough~~)
- Short paragraphs and lists

If you detect something emotionally extreme (e.g., suicidal thoughts, hopelessness):
- DO NOT try to solve the issue.
- Instead, end your message with [FLAG:CRISIS].`,
      },
      ...history.map((msg: any) => ({
        role: msg.role,
        content: msg.content.map((t: string) => ({ type: 'text', text: t })),
      })),
      {
        role: 'user',
        content: [{ type: 'text', text: message }],
      },
    ];

    const result = await generateText({
      model: google('gemini-1.5-flash'),
      messages,
      temperature: 0.7,
      providerOptions: {
        google: {
          safetySettings: [
            {
              category: 'HARM_CATEGORY_DANGEROUS_CONTENT',
              threshold: 'BLOCK_ONLY_HIGH',
            },
            {
              category: 'HARM_CATEGORY_HARASSMENT',
              threshold: 'BLOCK_MEDIUM_AND_ABOVE',
            },
          ],
        },
      },
    });

    const rawText = result.text;
    const isCrisis = rawText.includes('[FLAG:CRISIS]');
    const cleanText = rawText.replace('[FLAG:CRISIS]', '').trim();

    const responseChunks =
      cleanText.match(/[^.?!]+[.?!]/g)?.slice(0, 4).map((s) => s.trim()) || [
        cleanText,
      ];

    return NextResponse.json({ response: responseChunks, isCrisis });
  } catch (error) {
    console.error('API Error:', error);
    return NextResponse.json(
      { error: 'Failed to generate response' },
      { status: 500 }
    );
  }
}

```

### @/components/Helpline.tsx
```bash
// app/components/Helpline.tsx
export default function Helpline() {
  return (
    <div className="bg-red-100 p-4 border-l-4 border-red-500 text-red-700">
      <strong>⚠️ It seems you're going through something tough.</strong>
      <p>
        If you're in danger or in emotional distress, please reach out to a real person.
        <br />
        📞 Call: <button className=" border-2 rounded-2xl border-red-700 bg-red-300 text-black hover:bg-red-700 hover:text-white">1-800-123-4567</button> (Mock Hotline)
      </p>
    </div>
  );
}
```

