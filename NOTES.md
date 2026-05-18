# Backtrack Notes

## Player gesture follow-up

- Latest stable build as of 2026-05-17: the artwork pane flicker fix build, verified on-device after swiping from chapters/show notes back to artwork.
- Current smoothest build: artwork drag follows the finger horizontally and the release settles into the notes or chapters pane before the real page switches.
- Current gesture experiment removes the built-in pager from underneath the artwork pane and syncs the artwork to the final finger position before completing or canceling the drag.
- Flicker fix that worked: when returning from chapters/show notes to artwork, keep the real pager alive briefly with hit-testing disabled, then swap back to the standalone artwork pane after the gesture has settled.
- Artwork flicker fix that worked: replace `AsyncImage` in `RemoteArtworkView` with a small `URLSession` + `NSCache` image loader so recreated artwork views reuse the last loaded image instead of flashing the placeholder.
- Keep `stable-player-gesture-baseline` as the reliable rollback point if future gesture work gets worse.
