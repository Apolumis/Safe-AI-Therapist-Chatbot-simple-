## CHATBOT FOR MENTAL HEALTH - PREVENT EXTREME CASES
#### using google gemini safety setting.

### result:

```bash
✅ More effective crisis detection prompt
✅ Adjusted safety settings (less aggressive, so it won't block too early)
✅ Better structure, clearer comments
✅ Handles error cases (e.g., blocked or failed response)
```
#### Safety Settings
<hr/>
<table>
  <tr>
    <td><strong>1</strong></td>
    <td>HARM_CATEGORY_HATE_SPEECH</td>
    <td>Content that is rude, disrespectful, or profane.</td>
  </tr>
  <tr>
    <td><strong>2</strong></td>
    <td>HARM_CATEGORY_DANGEROUS_CONTENT</td>
    <td>Promotes, facilitates, or encourages harmful acts.</td>
  </tr>
  <tr>
    <td><strong>3</strong></td>
    <td>HARM_CATEGORY_HARASSMENT</td>
    <td>Negative or harmful comments targeting identity and/or protected attributes.</td>
  </tr>
  <tr>
    <td><strong>4</strong></td>
    <td>HARM_CATEGORY_SEXUALLY_EXPLICIT</td>
    <td>	Contains references to sexual acts or other lewd content./td>
  </tr>
</table>

#### Threshold
<hr/>
<table>
  <tr>
    <td><strong>Block none</strong></td>
    <td>BLOCK_NONE</td>
    <td>Always show regardless of probability of unsafe content</td>
  </tr>
  <tr>
    <td><strong>Block few</strong></td>
    <td>BLOCK_ONLY_HIGH</td>
    <td>Block when high probability of unsafe content</td>
  </tr>
  <tr>
    <td><strong>Block some</strong></td>
    <td>BLOCK_MEDIUM_AND_ABOVE</td>
    <td>Block when medium or high probability of unsafe content</td>
  </tr>
  <tr>
    <td><strong>Block most</strong></td>
    <td>BLOCK_LOW_AND_ABOVE</td>
    <td>	Block when low, medium or high probability of unsafe content</td>
  </tr>
  <tr>
    <td><strong>N/a</strong></td>
    <td>HARM_BLOCK_THRESHOLD_UNSPECIFIED</td>
    <td>Threshold is unspecified, block using default threshold</td>
  </tr>
</table>

#### config:
<hr/>
<table>
  <tr>
    <th>Temperature</th>
    <th>Behavior</th>
    <th>Use Case Example</th>
  </tr>
  <tr>
    <td><strong>0.0</strong></td>
    <td>Deterministic / factual</td>
    <td>Math, legal, summaries, safe therapist replies</td>
  </tr>
  <tr>
    <td><strong>0.3</strong></td>
    <td>Mostly safe, a little creative</td>
    <td>Chatbots with light emotion</td>
  </tr>
  <tr>
    <td><strong>0.7</strong></td>
    <td>Balanced (creative + smart)</td>
    <td>AI therapist, storytellers, emotional conversations</td>
  </tr>
  <tr>
    <td><strong>1.0</strong></td>
    <td>Very creative, less predictable</td>
    <td>Brainstorming, poetry, ideas, jokes</td>
  </tr>
</table>

### default temp for this AI is: _0.7_

- The model is empathetic and expressive.
- It maintains a balance between creativity and consistency.
- Responses feel caring and supportive, avoiding robotic or detached tones.
- Ideal for AI therapists, storytellers, and emotional conversations.

_This setting helps the AI respond thoughtfully while still ensuring accuracy._
