# Hacash Diamond Name Service

Inspired by ENS (Ethereum Name Service) on Ethereum, we propose a new proposal HIP-6, which we call HDNS (Hacash Diamond Name Service). The goal is to use the 6-digit literal value or number of the block diamond as the abbreviation or name of the diamond owner's account address, thereby simplifying the display form of the Hacash account address.

HIP-6 was first applied in the Hacash channel chain settlement network payment. Channel chain address is now in the form of: `12fEmV9HBRZfnfhypxmtW82TNHbCiHfkzU_cdfb81f2c55e814b03e1a33653666bc3_PaySev` (The "PaySev" suffix is the routing name of the channel node service provider). When the address is used for receive payment, you can remove the middle channel ID and change it to: `12fEmV9HBRZfnfhypxmtW82TNHbCiHfkzU_PaySev`.

As long as HDNS and the block diamond are used for DNS resolution, the address `12fEmV9HBRZfnfhypxmtW82TNHbCiHfkzU` can be referred to by its diamond name (e.g "KENYUU")  or diamond number (e.g #36189). Also, its address' form is more concise: `KENYUU_PaySev` or `36189_PaySev`.

HDNS-based short address resolution for mainnet transfers and web wallets allows greater security and error tolerance, and it considers that most of the HAC payments in the future will use the channel chain settlement network. HDNS for mainnet transfer will be launched at a suitable time in the future.