# Connectionless Data Exchange with wireless.

This p2p communication lowlevel library could broadcast messages to all the devices in certain areas, whether they are connected to different networks(eg, wifi), or not connected to any network.

This library has two sub-liraries, they're the slimulate API. the first simple one could only broadcast message to others. the second advance one could data exchange each other.

* wireless-simple
  * broadcast
  * listen
* wireless-advance

## wireless-simple

* `broadcast(char* msg)`: broadcast message once.
  * `msg`: the message to broadcast.
  * returns
    * Integer: 0 means successful.
* `listen(integer timeout)`: listen a message arrived until timeout.
  * `timeout`: the timout ms.
  * returns:
    * String: message string if successful.

the listener should:

* enter Wifi Monitor Mode
* Switch channels
* Provide 100ms timer interrupt at least.


