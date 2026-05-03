# Agent Werewolf — Demo Video Assets

Pre-made assets so you only need to (1) record dashboard playback,
(2) generate VO via TTS, (3) drop everything into CapCut. No design
work required.

## Quick start (15-minute video)

1. **Open `Convert-SVG-to-PNG.html`** in Chrome
2. Click "Download PNG" twice → you now have `architecture-diagram.png`
   and `end-card.png` (1920×1080)
3. **Open `VO-script-for-TTS.txt`** → copy each beat into a TTS service
   (ElevenLabs / OpenAI TTS / system text-to-speech). Save 6 MP3s:
   `beat1.mp3` … `beat6.mp3`
4. **Record dashboard footage**:
   - Open https://agent-werewolf.vercel.app in incognito
   - Use OBS or QuickTime to record the full archive replay (~3 min)
   - Save as `clipA-dashboard.mp4`
5. **Record terminal footage**:
   - Open `Clip-B-axl-witness-terminal.txt` → paste the static block into
     a terminal. Set font to 22pt, dark theme.
   - Record with OBS / QuickTime → save as `clipB-axl-witness.mp4`
   - Repeat with `Clip-D-verify-archive-terminal.txt` → `clipD-verify.mp4`
6. **Capture chainscan**:
   - Open https://chainscan-galileo.0g.ai/tx/0x8d63b8fb675cf3d771b4946dc375e1449fc5e116bb22b8bebb1e0641d66d142a
   - Record screen → save as `clipC-chainscan.mp4`
7. **Assemble in CapCut** (free, web or desktop):
   - Drop all clips on timeline at the timestamps in the script
   - Drop architecture-diagram.png as overlay 0:15–0:40
   - Drop end-card.png as final 5 seconds
   - Drop beat1-6 mp3s on audio track at the timestamps
   - Export 1080p H.264 MP4
8. **Upload to Loom or YouTube unlisted**, paste the link into
   `SUBMISSION.md` line "Demo video".

## File index

| File | Purpose | Used at |
|------|---------|---------|
| `hook-card.svg` / `.png` | Opening title with 8 agent dots | **0:00–0:20** |
| `architecture-diagram.svg` / `.png` | 4-component infrastructure overview | 0:15–0:40 |
| `end-card.svg` / `.png` | Closing branded card | 2:55–3:00 |
| `Convert-SVG-to-PNG.html` | Browser-based SVG→PNG converter (3 cards) | Run once before editing |
| `VO-script-for-TTS.txt` | 6-beat voice-over text | Generate audio |
| `Clip-B-axl-witness-terminal.txt` | Terminal output for AXL beat | 1:50–2:15 |
| `Clip-D-verify-archive-terminal.txt` | Terminal output for verification beat | 2:15–2:40 |

## TTS recommendations

| Service | Voice | Cost | Notes |
|---------|-------|------|-------|
| **ElevenLabs** | Adam, Bill, Antoni | Free tier ≈ 10k chars | Best quality, low effort |
| **OpenAI TTS** | onyx, alloy | $0.015 / 1k chars | Crisp, professional |
| **PlayHT** | Multiple | Free trial | Good fallback |
| **macOS `say`** | Alex, Daniel | Free | Robotic but acceptable |

For the script length (~700 words total), free tiers are sufficient.

## Dashboard recording tips

- Open in **incognito** so no extensions / bookmarks bar visible
- Browser zoom **110%** so text is readable in 1080p
- Hide bookmarks bar (Ctrl+Shift+B in Chrome)
- Full-screen the browser (F11) to remove the address bar from frame
- The replay starts ~10 seconds after the page loads (waits for WebSocket
  to fail). Start recording AFTER replay begins.
- The replay runs at 4× the original game speed — already compressed.
  Game completes in ~60 seconds of footage.

## Backup plan if anything fails

- **Can't get TTS to sound right?** Record VO with phone mic in a quiet
  room (closet works). Audio quality is secondary to clarity.
- **Can't run dashboard locally?** Use the public URL — already deployed.
- **Editor too complex?** Use Loom: it auto-records screen + mic, gives
  share link in 30 s. No editing needed. Just narrate live over the
  dashboard playback.

## Final submission checklist

- [ ] Video uploaded (Loom/YouTube unlisted), link copied
- [ ] Vercel auth disabled (https://vercel.com/muhammad-meidy-noor-al-barrys-projects/dashboard/settings/deployment-protection)
- [ ] Dashboard tested in incognito — replays without login
- [ ] `SUBMISSION.md` "Demo video" link updated
- [ ] All 8 GitHub repos public
- [ ] ETHGlobal submission form filled out with the 3 tracks
