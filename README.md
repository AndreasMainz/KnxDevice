# KNX Bus Device library for Arduino with integrated Busmonitor

## Links :
- [Blog](http://www.liwan.fr/KnxWithArduino/)
- [GitHub Page](http://franckmarini.github.io/KnxDevice)
- [KNX Association](http://www.knx.org)
- [Siemens KNX chipsets](http://www.buildingtechnologies.siemens.com/bt/global/en/buildingautomation-hvac/gamma-building-control/gamma-b2b/Pages/transceivers.aspx)

## Realization examples :
- See the Realizations page in the [Blog](http://www.liwan.fr/KnxWithArduino/).

NB : The source code is available in the "examples" folder.

## Changes on fork :
I changed the software in that way, index 0 is always a reveived message which is not Adressed to the device.
So index 0 acts like a groupmonitor all other indexes work like usally.

Since my c++ skills are limited and i had to get data from the tpuart layer to the arduino sketch,
i added some global data where the raw messages and the destination address is stored.
But if anyone has a proposal to improve architecture..

So com object list looks like:
KnxComObject KnxDevice::_comObjectsList[] = {
  
  ### /* Index 0 */ KnxComObject( Knx_In.GMonitor,       KNX_DPT_1_001 , COM_OBJ_LOGIC_IN), // all not adressed GA's willl be mapped to this index
  /* Index 1 */ KnxComObject( Knx_In.R1_hoch_runter, KNX_DPT_1_001 , COM_OBJ_LOGIC_IN),
  /* Index 2 */ KnxComObject( Knx_In.R1_stop,        KNX_DPT_1_001 , COM_OBJ_LOGIC_IN),
  
  additional global vars are defined: (should be added to your sketch)
  
  * uint8_t Ga_data[20]; // holding the raw data received
  * uint16_t Ga_int;     // holding the group address of the received message

## Changes in detail

* for the rx State Machine i added a new state: RX_EIB_TELEGRAM_RECEPTION_OWN_MESSAGE
to distinguish between own message which was send some ms before and the messages which havn't be addressed to the device.
Both had been mapped to RX_EIB_TELEGRAM_RECEPTION_NOT_ADDRESSED before, now it is separated.

* The messages going to RX_EIB_TELEGRAM_RECEPTION_NOT_ADDRESSED are now mapped to the already existing Comobject index 0
  
