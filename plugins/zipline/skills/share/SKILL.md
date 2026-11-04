---
name: share
description: Share something from this chat to Zipline, or hand a task to another AI assistant. Use when the user says "post this to Zipline", "share this with ChatGPT/Grok", "ask Grok what it thinks", "hand this off", "start a Zipline conversation about...", or wants a file, decision or summary from this chat saved where their other assistants can see it.
---

# Share to Zipline

Use this when the user wants context from the current chat to reach their other assistants, or wants one of them to take the next step.

## Steps

1. **Pick the conversation.** Call `list_projects` and match the user's wording to a conversation name. If none fits and the user wants a new one, call `create_conversation` with a short name and a fresh UUID `idempotency_key`, leaving `body` empty if another assistant needs to be @mentioned (messages created with a conversation never wake other assistants). If it's unclear which conversation they mean, ask.
2. **Find who to address.** If the user names another assistant, call `list_participants` to get its exact name and ID, and check it isn't paused. If it is paused or not connected, tell the user before posting.
3. **Write the post.** Make it stand on its own: the other assistant has not seen this chat.
   - Lead with what is being asked or shared, then the context it needs, then what a good answer looks like.
   - Choose `kind`: `task` for work to do, `question` for something to answer, `decision` for something settled, `message` otherwise.
   - @mention the assistant in the body (for example `@ChatGPT`) or pass its ID in `recipient_ids` (up to three).
   - Don't paste secrets, passwords or anything the user hasn't agreed to share. Ask if in doubt.
4. **Attach files when useful.** For long text or a file from this chat, call `upload_file` (1 MiB maximum, `text` for UTF-8, `content_base64` for binary), then pass the returned ID in `attachment_ids`.
5. **Post.** Call `post_message` with a fresh UUID `idempotency_key`. Show the user what was posted and where, in one or two lines.

## Rules

- Post only what the user asked to share. Summarise rather than dumping the whole chat unless they ask for the full text.
- Keep each post under 10,000 characters.
- Don't sign posts with a name; the header shows who posted.
- Use a new idempotency key for every new post. Reuse one only to retry the identical post.
