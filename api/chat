const DEFAULT_MODEL = "gemini-2.5-flash";

module.exports = async function handler(req, res) {
  res.setHeader("Cache-Control", "no-store");
  res.setHeader("X-Content-Type-Options", "nosniff");

  if (req.method !== "POST") {
    res.setHeader("Allow", "POST");
    return res.status(405).json({ error: "Use POST for chat requests." });
  }

  const apiKey = process.env.GEMINI_API_KEY;
  if (!apiKey) {
    return res.status(500).json({
      error: "AI is not configured yet. Add GEMINI_API_KEY in Vercel Environment Variables, then redeploy."
    });
  }

  try {
    const body = req.body || {};
    const message = typeof body.message === "string" ? body.message.trim() : "";
    const history = Array.isArray(body.history) ? body.history : [];

    if (!message) return res.status(400).json({ error: "Please enter a message." });
    if (message.length > 8000) return res.status(400).json({ error: "Message is too long. Please keep it under 8,000 characters." });

    const contents = [];
    for (const item of history.slice(-12)) {
      if (!item || !["user", "model"].includes(item.role) || typeof item.text !== "string") continue;
      const text = item.text.trim().slice(0, 8000);
      if (text) contents.push({ role: item.role, parts: [{ text }] });
    }
    contents.push({ role: "user", parts: [{ text: message }] });

    const model = (process.env.GEMINI_MODEL || DEFAULT_MODEL).trim();
    const url = `https://generativelanguage.googleapis.com/v1beta/models/${encodeURIComponent(model)}:generateContent?key=${encodeURIComponent(apiKey)}`;
    const upstream = await fetch(url, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        systemInstruction: {
          parts: [{ text: "You are Dundumon AI, a helpful and honest AI assistant. Reply in Malayalam or Manglish when the user uses Malayalam/Manglish; otherwise reply in the user's language. Use clear formatting. Do not claim to have performed live web searches. If unsure, say so." }]
        },
        contents,
        generationConfig: { temperature: 0.7, maxOutputTokens: 1800 }
      })
    });

    const data = await upstream.json().catch(() => ({}));
    if (!upstream.ok) {
      const messageFromProvider = data?.error?.message;
      console.error("Gemini API error:", upstream.status, messageFromProvider || "unknown error");
      return res.status(upstream.status === 429 ? 429 : 502).json({
        error: upstream.status === 429
          ? "The free AI quota may be temporarily exhausted. Please wait and try again later."
          : "The AI provider could not complete this request. Check your API key, model setting, and provider status."
      });
    }

    const answer = (data?.candidates?.[0]?.content?.parts || [])
      .map(part => typeof part.text === "string" ? part.text : "")
      .join("\n")
      .trim();

    if (!answer) return res.status(502).json({ error: "The AI returned an empty response. Please try again." });
    return res.status(200).json({ answer });
  } catch (error) {
    console.error("Chat route error:", error?.message || error);
    return res.status(500).json({ error: "Something went wrong while contacting the AI. Please try again." });
  }
};
