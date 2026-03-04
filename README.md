OPENAI_API_KEYsk-proj-dVLtT6jfu1AaW2eKuV1uv-2n_BeTrXKjAf177iuNBF5aVfc2DNEFJIIX3mVRFGfy6Dexvbe9bDT3BlbkFJonL25ZZQKKmCw6ETSDJwHP0oYvGj_hkzQxvcj9fSvnbn8m8Sf3AISuv-AuNIqGh_2s5rjaEFQA<!DOCTYPE html>
<html>
<head>
  <title>AI Ad Generator Pro</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body { font-family: Arial; background:#0f172a; color:white; text-align:center; padding:20px; }
    .box { background:#1e293b; padding:20px; border-radius:10px; margin-top:20px; }
    input, textarea { width:90%; padding:10px; margin:8px; border-radius:5px; border:none; }
    button { padding:10px 20px; background:#22c55e; border:none; border-radius:5px; color:white; font-size:16px; }
    #output { white-space: pre-wrap; margin-top:20px; }
  </style>
</head>
<body>

<h1>🚀 AI Ad Generator PRO</h1>

<div class="box">
  <input type="text" id="business" placeholder="Business Name"><br>
  <input type="text" id="product" placeholder="Product / Service"><br>
  <input type="text" id="offer" placeholder="Offer"><br>
  <input type="text" id="audience" placeholder="Target Audience"><br>
  <input type="text" id="city" placeholder="City"><br>

  <button onclick="generateAd()">Generate Ad</button>

  <div id="output"></div>
</div>

<script>
async function generateAd() {
  const business = document.getElementById("business").value;
  const product = document.getElementById("product").value;
  const offer = document.getElementById("offer").value;
  const audience = document.getElementById("audience").value;
  const city = document.getElementById("city").value;

  document.getElementById("output").innerText = "Generating...";

  const response = await fetch("/api/generate", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ business, product, offer, audience, city })
  });

  const data = await response.json();

  if (data.result) {
    document.getElementById("output").innerText = data.result;
  } else {
    document.getElementById("output").innerText = "Error: " + data.error;
  }
}
</script>

</body>
</html>export default async function handler(req, res) {
  if (req.method !== "POST") {
    return res.status(405).json({ message: "Method not allowed" });
  }

  const { business, product, offer, audience, city } = req.body;

  const prompt = `
Create a powerful Instagram and WhatsApp marketing ad.

Business: ${business}
Product: ${product}
Offer: ${offer}
Audience: ${audience}
City: ${city}

Give:
1. Reel Hook
2. Short Script
3. Caption
4. 10 Hashtags
5. WhatsApp Promo Message
`;

  try {
    const response = await fetch("https://api.openai.com/v1/chat/completions", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Authorization: `Bearer ${process.env.OPENAI_API_KEY}`,
      },
      body: JSON.stringify({
        model: "gpt-4o-mini",
        messages: [{ role: "user", content: prompt }],
      }),
    });

    const data = await response.json();

    if (!data.choices) {
      return res.status(500).json({ error: "OpenAI response error" });
    }

    res.status(200).json({
      result: data.choices[0].message.content,
    });

  } catch (error) {
    res.status(500).json({ error: "Server error" });
  }
}
