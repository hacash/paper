HIP-15: HACD Inscription
===

This document is a compilation and summary of the community's discussions on the HIP-15, including technical and economic models, with the goal of developing a compendium that can be used to guide technology development.


HIP-15 Technical Aspects:

1. Each HACD can be attached (inscribed) with a piece of text (or binary) data that can be used by the artist to bind a new art form or other function

2. The length of each inscription is limited to 64bytes (below this length is supported)

3. Inscriptions can be superimposed, new inscriptions are inscribed, and the list of old inscriptions remains and accessible

4. Each HACD can carry up to 200 inscriptions

5. A single HACD has only one chance to be engraved within 1000 block times (about 3.5 days).

6. Batch engraving is possible, up to 200 HACDs can be supported at a time (inscribe the same content, and check the engraving opportunities in turn)

7. All inscriptions can be "erased" (just changing the status data of the HACD, while the block history is still retained)

8. Erasure will only discard all inscriptions at once, not selectively erase some of them

HIP-15 Economic Model Aspects:

1. Each HACD has 10 chances for "free" inscriptions (meaning that the agreement fee is waived, and the gas fee is still required)

2. Starting from the 11th time, 1/10 of the amount of the "HACD Average Bid Burn" amount needs to be burned as the system protocol fee to balance the occupation of blockchain space and contribute to the supply regulation of HAC

3. No matter how many inscriptions have already been inscribed, you can pay the full amount of the "HACD Average Bid Burn" in one lump sum to erase all inscriptions and get 10 "free" chances again

4. Only the corresponding protocol fee needs to be destroyed, and the inscription and erasure actions can be repeated without limitation

5. The inscription and erasure of the HACD will destroy 90% of the gas fee (same as the HACD bid)

