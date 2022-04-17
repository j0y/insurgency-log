# insurgency-log

Go package for parsing insurgency server logfiles. It exports types for insurgency logfiles, their regular expressions, a function for parsing and a function for converting to non-html-escaped JSON. 

## Usage

For more examples look at the [tests](./insurgencylog_test.go) and the command-line utility in [examples folder](./example).

```go
package main

import (
  "fmt"

  insurgencylog "github.com/j0y/insurgency-log"
)

func main() {

  var msg insurgencylog.Message

  // a line from a server logfile
  line := `L 11/05/2018 - 15:44:36: "Player<12><STEAM_1:1:0101011><CT>" purchased "m4a1"`

  // parse into Message
  msg, err := insurgencylog.Parse(line)

  if err != nil {
    panic(err)
  }

  fmt.Println(msg.GetType(), msg.GetTime().String())

  // cast Message interface to PlayerPurchase type
  playerPurchase, ok := msg.(insurgencylog.PlayerPurchase)

  if ok != true {
    panic("casting failed")
  }

  fmt.Println(playerPurchase.Player.SteamID, playerPurchase.Item)

  // get json non-htmlescaped
  jsn := insurgencylog.ToJSON(msg) 

  fmt.Println(jsn)
}
```
Example JSON output:
```json
{
  "time": "2018-11-05T15:44:36Z",
  "type": "PlayerPurchase",
  "player": {
    "name": "Player",
    "id": 12,
    "steam_id": "STEAM_1:1:0101011",
    "side": "CT"
  },
  "item": "m4a1"
}
```