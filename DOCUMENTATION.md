# Bribefile Documentation

Bribefiles are the data storage for the [Beetswars.live](https://www.beetswars.live/) website. While voting is active, incentive offers are listed there by volunteers. Bribefiles are published under [MIT-License](./LICENSE)

**All offers are subject to change until voting closes. We work with care, but errors can not always be avoided. Please do your own research, read original announcements on Discord or Twitter. This is not financial advice!**

To contribute to the project, please read the following documentation carefully!

All values marked with an asterisk (\*) are optional.
**Please note:** When publishing vote-data file before voting started, please leave bribedata array empty!

The bribe-data files are noted as a JSON object, containing exactly one level-0 entry. For each round an new file is created. 

## level 0

This is the main object for the voting round, containing general information about the round.

`"version"` - Version number of the data file, using semantic versioning: first part represents the voting round, second part represents the count of incentive offers, third part counts the version without adding new offers, starting with 0  
`"snapshot"` - Hexadecimal key for the snapshot vote. This string can be extracted from the snapshot.org URL for the actual vote. It always starts with "0x" followed by 64 hexadecimal digits.  
`"description"` - Contains the headline from the snapshot vote.  
`"round"` - Integer value for the described round.  
`"tokendata"` \* - Array of Tokens (see level 1)  
`"bribedata"` \* - Array of Incentive offers (see level 1)

## level 1 - tokendata

This array contains token information to calculate USD price, if offers contain a fixed number of tokens instead of USD value.

`"token"` - short name for the Token, like shown in brackets on coingecko.com or in the ERC-20 token list on ftmscan.com  
`"tokenaddress"` \* - String contains hexadecimal value of the token's smart contract starting with "0x" followed by 40 hexadecimal digits. Info can be found on coingecko.com (make sure you chose the Fantom network), on ftmscan or in the token project documentation.  
`"coingeckoid"` \* - String contains the API ID from coingecko.com  
`"bptpoolid"` \* - String contains the pool ID for a liquidity pool on beets.fi - this ID can be found in the pool's URL starting with "0x" followed by 64 hexadecimal numbers  
`"isbpt"` \* - Boolean, set to true, if the bribe is a pool instead of a single token  
`"lastprice"` \* - Number, can hold a manual entry for the price of a pool at vote ending, if no historic price available  
  
For a single token, tokenadress and coingeckoid are mandatory, for a pool bptpoolid is mandatory.

## level 1 - bribedata

This array contains the incentive offers, one entry for each rewarded pool. 

`"voteindex"` - Numeric value representing the position of the incentivised pool within the voting list on snapshot.org. Counting starts with 0, so the first offer has 0, the second offer has 1 ...  
`"poolname"` - Short name for the pool, like displayed on the voting list on snapshot.org  
`"poolurl"` - String contains the full URL for the pool website on beets.fi, always starting with "https://beets.fi/pool"  
`"rewarddescription"` - String contains a short description for the offer, best copied from the announcement on twitter.  
`"assumption"` - String contains any restrictions to the offer, if given. Can be an empty string.  
`"percentagethreshold"` \* - Floating point value, offer is only available, if the voting percentage is at or over the given value. Only applicable to the "percent" reward type. Use this value, if the percent reward is combined with "fixed" or "pervote" rewards within the same offer. For single "percent" offers with threshold use "payoutthreshold" instead.   
`"rewardcap"` \* - Number. Overall reward is capped at the given value in USD or in native tokens, if not USD based.  
`"additionalrewards"` \* - Array of additional rewards offered, calculated with a factor of the original reward. See level 2.  
`"reward"` - Array of any rewards. This is the current file format and needs to be available for newer app versions.  See level 2.
`"payoutthreshold"` \* - Floating point value, offer is only available, if the voting percentage is at or over the given value. Only applicable to the "percent" reward type. Use this value, if there is only one "percent" reward and no other reward types for the offer. This will display a warning, if threshold is not reached. Special usecase: Set this parameter to `-1` to invalidate the "below 0.15%" check on a "percent" reward.  
  
For the current file format, the `"reward"` array must contain at least one entry.

## level 2 - reward

General description for any fixed, per percent or per vote reward. **This new file format has replaced the single reward types in the app.** Please do not use "pervotereward", "percentreward" or "fixedreward" any longer. Each offer can contain one or more reward entries.  
  
`"type"` - String, one of ["fixed", "percent", "pervote"]  
`"token"` - short name for the Token, like shown in brackets on coingecko.com or in the ERC-20 token list on ftmscan.com. Must be identical to the "token" in tokendata, if price must be calculated  
`"amount"` - Number representing the offered tokens or the USD value of the offered tokens overall ("fixed"), per percent of votes ("percent") or per one single fBEETS vote ("pervote").  
`"isfixed"` - Boolean. If true, the offer is made as an USD value, no matter what tokens or pool-tokens are offered. Always true if token is "USDC". If false, the given amount is the count of "token" offered. Dollar value is calculated on the fly. In this case, "tokendata" for the given token must be filled.  
~~"rewardcap"~~ - moved up to the bribedata object. Please do not use here.  
~~"percentagethreshold"~~ - moved up to the bribedata object. Please do not use here.  

## level 2 - additionalrewards \* 

This is a special case for the new CRE8R incentivizing system, where you can get higher payout triggered by actions not under control of beethoven-x or beetswars.live. Only makes sense in combination with "percenterward", cannot stand alone.  
  
`"tier"` - String representing the name of the special offer.  
`"factor"` - Floating point number. The offer's base value is multiplied with this factor.

## Styleguide

* Use 2 spaces for indentation
* Put strings in double quotes
* Put empty arrays [] or objects {} in a single line
* Write key in double quotes, followed by a colon, a space and the value.
* If the value is an array, opening bracket is at the end of the line, array values start at the next line, followed by a comma
* If a value is an object (dictionary), the key-value pairs stand in a single line each, opening and closing brackets go in extra lines above and below

## Links
* [Coingecko](https://www.coingecko.com/) is our provider for historic data.
* List of Pools on [Beethoven-x](https://beets.fi/#/pools) - newly added pools can be found under "Community pools"
* Voting is published on [Snapshot.org](https://snapshot.org/#/beets.eth)

## Version
Documentation version 0.2 published on 2022/08/17  
V 0.3 updated on 2022/08/30 - new file format activated  
V 0.4 on 2022/08/30 - minor updates  
V 0.5 on 2022/09/03 - add rule to deactivate 0.15% check  
