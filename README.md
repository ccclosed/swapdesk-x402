# SwapDesk — an x402 use case for Swap.io's Agent API

Submission for the Superteam Earn bounty **“Make a video about our x402 Feature”** (sponsor: swap.io, 500 USDC pool).

**Video (105 s, 1920×1080):** https://github.com/ccclosed/swapdesk-x402/releases/download/v1.0.0/swapdesk_x402.mp4

SwapDesk is a concept: a Telegram bot for a trading community that sells swap quotes for
**1¢ per answer** and buys every quote from **Swap.io's Agent API** (`hermes.swap.io`)
for **~0.3¢** using **x402**. The bot is an x402 seller to its users and an x402 buyer to
Swap.io at the same time — a real agent-to-agent margin business with no API keys and no
subscription.

## Why x402 fits this use case

- **No signup, no key to leak.** The bot has nothing to provision and nothing for a user to steal.
- **Cost tracks usage exactly.** Quote only when a member asks: 100 questions = 100 payments of $0.003.
  A bot that is idle costs $0. An API-key plan charges for capacity it never uses.
- **Agent-to-agent commerce.** The same HTTP 402 pattern works in both directions: the bot charges
  its users and pays Swap.io, keeping a ~0.7¢ margin per answer.
- **Budget caps per request.** The payer decides the maximum per call, so a runaway agent cannot
  burn a subscription; failed/unpaid requests are never charged.

## The x402 flow in one sentence

The agent makes an HTTP request → the server answers **402 Payment Required** with the exact price,
asset, network and payTo address → the agent pays in **USDC** (gasless: the facilitator's fee payer
covers the network fee) and retries the same request with an `X-PAYMENT` header → **200 OK** with the
ranked quotes and an on-chain settlement receipt in `X-PAYMENT-RESPONSE`.

## What is real in this video, and what is illustrated

**Real (captured live on 2026-11-11 20:18 MSK / 17:18 UTC):**

```
$ curl -s -i -X POST https://hermes.swap.io/v1/swap/quote \
    -H 'Content-Type: application/json' \
    -d '{"input":{"token":"So11111111111111111111111111111111111111112","amount":"1000000000","chainKey":"sol"},
         "output":{"token":"EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v","chainKey":"sol"},
         "userAddress":"9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM","slippageBps":50}'

HTTP/2 402
content-type: application/json

{
  "x402Version": 1,
  "accepts": [{
    "scheme": "exact",
    "network": "solana",
    "maxAmountRequired": "3007",
    "resource": "https://hermes.swap.io/v1/swap/quote",
    "asset": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
    "payTo": "BPz2u6eoWLervxEqfpsMCVmKk7h6fEVsd7L5rvEibMfD",
    "extra": {"feePayer": "2wKupLR9q6wXYppw8Gr2NvWxKBUqm4PPJKkQfoxHDBg4"}
  }]
}
```

That is the exact, unedited server response — price **3007 atomic USDC = $0.003007**, asset = USDC
mint, Solana network, payTo = Swap.io fee wallet, feePayer covers gas.

**Illustrated, not executed:** the USDC payment leg. This build environment had no funded wallet
(checked: 0 USDC on Solana, 0 USDC on Base), so no paid call, no settlement receipt and no Solscan
link are claimed. The video labels this on screen (`HONEST LABEL`) and in the narration.

Reproduce the challenge yourself (free, no payment):

```
curl -s -i -X POST https://hermes.swap.io/v1/swap/quote \
  -H 'Content-Type: application/json' \
  -d '{"input":{"token":"So11111111111111111111111111111111111111112","amount":"1000000000","chainKey":"sol"},
       "output":{"token":"EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v","chainKey":"sol"},
       "userAddress":"9WzDXwBbmkg8ZTbNMqUxvQRAyrZzDsGYdLVL9zYtAWWM","slippageBps":50}'
```

More: `curl -s https://hermes.swap.io/v1/info` (machine-readable endpoint list, prices and x402 terms)
· docs: <https://docs.swap.io/developer-tools/agent-api-x402/quickstart> · protocol: <https://x402.org>

## Files

| File | What |
|------|------|
| `swapdesk_x402.mp4` | the 105 s video (1080p, 24 fps, AAC 192 kbps) |
| `script.md` | narration, captions, exact timings |
| `storyboard.md` | visual design + motion of the 8 beats |
| `capture_live.sh` | the exact script that captured the real 402 (run on 2026-11-11) |
| `capture_transcript.txt` | terminal transcript of that live capture |
| `build/` | the whole production pipeline: PIL frame renderer, piper TTS, numpy music bed, ffmpeg assembly |

Made for the Swap.io x402 bounty. Links: <https://hermes.swap.io> · <https://swap.io> · <https://x402.org>