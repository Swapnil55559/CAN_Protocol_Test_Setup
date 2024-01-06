# CAN Protocol Test Setup

This repository contains the STM32 CUBEIDE Code base for CAN (Controller Area Network) Protocol Test Setup for different Baud Rates (100KBps, 250KBps and 500KBps), in which data packet of 2 byte has been send, the Data transmission has been taken between two STM32F303K8 microcontrollers which has 1 CAN Controller (bxCAN)
Working in Normal Mode. The repository contains Two files, one for Transmitter and another for Receiver.

Any High speed CAN Tranceiver can be used, in this case TJA1050 CAN Tranceiver is used

#### Transmitter:

 - Transmitter sends Two bytes of Data (DLC = 2), No extended ID has been used.
 - The standard ID is 0x303, The Builti-In GPIO Led will Toggle on Data transmission
 - Data will be send on External interrupt on rising edge in GPIO Pin PB6

#### Embedded Code:

 - Message will be read when RxFIFO0 interrupt triggers (CAN RX0 interrupt is enabled)
 - First Data byte contains the number of times LED shall Toggle and second Data byte contains what should be the delay between each blinks
 - Filter Banks has been used to filter out only ID 0x303, CAN Filter FIFO0 has been used

## Connections

* Connect the CAN Rx (PA11 or D10) and CAN Tx (PA12 or D2) to the Rx Pin and Tx pin of the Tranceiver respectively in both the STM32F303K8
* Provide 3.3V VCC and GND to Tranceiver from the STM32F303K8
* Connect CAN H and CAN L of one Tranceiver to CAN H and CAN L of another Tranceiver in a twisted pair of wire
* External Switch and push button can be attached to the pin 5 & 6 for direction control and to apply brake respectively.

## Instructions

In order to operate on 100KBps, 250KBps or 500KBps, Just configure `hcan.Init.Prescaler` to 20, 8 or 4 respectively. Configuration can be done inside the CubeMX, navigate to: **connectivity -> CAN -> Parameter Settings -> Prescaler (for Time Quantum)** or It can also be configured from `main.c` file inside the **Src** file

For Filter Configuration, configure the following according to the ID needed to be filtered
```C++
  /* USER CODE BEGIN CAN_Init 2 */
  CAN_FilterTypeDef canfilterconfig;

  canfilterconfig.FilterActivation = CAN_FILTER_ENABLE;
  canfilterconfig.FilterBank = 10;
  canfilterconfig.FilterFIFOAssignment = CAN_FILTER_FIFO0;
  canfilterconfig.FilterIdHigh = 0x303<<5;
  canfilterconfig.FilterIdLow = 0x0000;
  canfilterconfig.FilterMaskIdHigh = 0x303<<5;
  canfilterconfig.FilterMaskIdLow = 0x0000;
  canfilterconfig.FilterMode = CAN_FILTERMODE_IDMASK;
  canfilterconfig.FilterScale = CAN_FILTERSCALE_32BIT;
  canfilterconfig.SlaveStartFilterBank = 0;

  HAL_CAN_ConfigFilter(&hcan,&canfilterconfig);
  /* USER CODE END CAN_Init 2 */
```
