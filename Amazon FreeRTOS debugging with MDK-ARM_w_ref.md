# Amazon FreeRTOS Events with MDK-ARM on STMicroelectronics STM32L4 Discovery kit IoT node (B-L475E-IOT board)

1.  Download [**Amazon FreeRTOS Device Software**](https://github.com/MDK-Packs/amazon-freertos) and extract it to folder which will be referred as *BASE_FOLDER*
2.  Open &#181;Vision project **aws_demos.uvprojx** located in folder *<BASE_FOLDER>*\demos\st\stm32l475_discovery\mdk\
3.  If Keil.STM32L4xx_DFP pack is not installed **Pack Installer** will pop-up and you should click on **Install** button to 
    install the missing packs [Install missing packs](#install-missing-packs), if Pack Installer does not automatically pop-up, start 
    Pack Installer from &#181;Vision by clicking on **Pack Installer** button [Start Pack Installer](#start-pack-installer)
4.  From **Pack Installer** install packs specified below [Install packs](#install-packs) and close Pack Installer after all 
    necessary packs are installed, also confirm Reload Packs in &#181;Vision :
    - **Keil::STM32L4xx_DFP** pack
    - **ARM::CMSIS** pack
    - **ARM::CMSIS-FreeRTOS - 9.1.0** pack, in pop-up window **Pack Unzip ARM::CMSIS-FreeRTOS** check **I agree to all the terms of the preceding License Agreement** 
      and click on **Next** button [Install ARM::CMSIS-FreeRTOS](#install-arm::cmsis-freertos)
    - **Keil::ARM_Compiler** pack
5.  In &#181;Vision **build** project (**F7**) to confirm there are no problems [Initial project build](#initial-project-build)
6.  In &#181;Vision open **Manage Run-Time Environment** [Start RTE configuration](#start-rte-configuration) and enable 
    Software Components **CMSIS::CORE** and **Compiler::Event Recorder** and click **OK** button [Select Software Components](#select-software-components)
7.  Copy **freertos_evr.h** file from *<MDK_root_folder>*\ARM\PACK\ARM\CMSIS-FreeRTOS\9.1.0\CMSIS\RTOS2\FreeRTOS\Include\ folder 
    to *<BASE_FOLDER>*\demos\st\stm32l475_discovery\mdk\ folder
8.  Copy **freertos_evr.c** file from *<MDK_root_folder>*\ARM\PACK\ARM\CMSIS-FreeRTOS\9.1.0\CMSIS\RTOS2\FreeRTOS\Source\ folder 
    to *<BASE_FOLDER>*\demos\st\stm32l475_discovery\mdk\ folder
9.  In &#181;Vision add **freertos_evr.c** source file to project (for example to **app** group) right click on app group and select 
    **Add Existing Files to Group \'app\'** **.****.****.**, select **freertos_evr.c** and click on **Add** button 
    [Add freertos_evr.c file to project](#add-freertos_evrc-file-to-project) and click on **Close** button to close the Add Files dialog
10. In &#181;Vision open **Options for Target** dialog and under **C/C++ (AC6)** tab add include path to project 
    root folder **.\\** [Add include path](#add-include-path) and click on **OK** button to close Options for Target dialog
11. In &#181;Vision double click on **FreeRTOSConfig.h** file in project tree under **app_config** group and 
    insert:<br/> `#include "freertos_evr.h"`<br/> [Add include to FreeRTOSConfig.h file](#add-include-to-freertosconfigh-file)
12. In &#181;Vision double click on **main.c** file in project tree under **app** group and 
    insert:<br/> `#include "EventRecorder.h"`<br/> befor main function, and 
    insert:<br/> `EventRecorderInitialize(EventRecordAll, 1);`<br/> at beginning of the main function [Update main.c file](#update-mainc-file)
13. Connect USB cable to **micro USB connector ST LINK** on B-L475E-IOT board
14. In &#181;Vision open **Options for Target** dialog and under **Debug** tab click on **Manage Component Viewer Description Files** **.****.****.** button and 
    in Manage Component Viewer Description Files add **FreeRTOS.scvd** file found 
    in *<MDK_root_folder>*\ARM\PACK\ARM\CMSIS-FreeRTOS\9.1.0\CMSIS\RTOS2\FreeRTOS\ click on **Open** button, and click on **OK** button 
    to close Manage Component Viewer Description Files and once more click on **OK** button to close Options for Target dialog [Add FreeRTOS.scvd file](#add-freertosscvd-file)
15. In &#181;Vision re-**build** project (**F7**)
16. In &#181;Vision start the **Debug session (Ctrl + F5)** [Start/Stop Debug session](#startstop-debug-session)
17. In &#181;Vision open **Event Recorder Window** [Open Event Recorder Window](#open-event-recorder-window)
18. In &#181;Vision **start the code execution** [Start code execution](#start-code-execution)
19. In &#181;Vision in **Event Recorder Window** you should see **FreeRTOS Events** [FreeRTOS Events](#freertos-events)

# Re-targeting UART printf debug to Event Recorder
20. In &#181;Vision stop the **Debug session (Ctrl + F5)** [Start/Stop Debug session](#startstop-debug-session)
21. In &#181;Vision remove from **app** group **retarget_io.c** file, select the file and right click and select **Remove File \'retarget_io.c\'** and confirm removal
21. In &#181;Vision double click on **main.c** file in project tree under **app** group and replace function :
    ```
    void vMainUARTPrintString( char * pcString )
    {
         const uint32_t ulTimeout = 3000UL;

         HAL_UART_Transmit( &xConsoleUart,
                            ( uint8_t * ) pcString,
                            strlen( pcString ),
                            ulTimeout );
    }
    ```
    with :
    ```
    void vMainUARTPrintString( char * pcString )
    {
      uint32_t i;

      for (i = 0; i < strlen(pcString); i++) {
        putchar(pcString[i]);
      }
    }
    ```
    [Update vMainUARTPrintString function in main.c file](#update-vmainuartprintstring-function-in-mainc-file)
22. In &#181;Vision in **main.c** remove function :
    ```
    void * malloc( size_t xSize )
    {
        configASSERT( xSize == ~0 );

        return NULL;
    }
    ```
23. In &#181;Vision open **Manage Run-Time Environment** [Start RTE configuration](#start-rte-configuration) and for Software Component 
    **Compiler::I/O::STDOUT** select **EVR** and click **OK** button [Re-target STDOUT to Event Recorder](#re-target-stdout-to-event-recorder)
24. In &#181;Vision click on **Compiler** group in project tree and expand it, then click on **EventRecorderConf.h (Event Recorder)** file, 
    click on **Configuration Wizard** tab and select **256** instead of 64 for **Number of Records** [Edit Event Recorder Configuration](#edit-event-recorder-configuration)
25. In &#181;Vision re-**build** project (**F7**)
26. In &#181;Vision start the **Debug session (Ctrl + F5)** [Start/Stop Debug session](#startstop-debug-session)
27. In &#181;Vision open **Debug (printf) Viewer** [Open Debug (printf) Viewer](#open-debug-printf-viewer)
28. In &#181;Vision in **Debug (printf) Viewer** you should see **Debug Output** [Debug Output in Debug (printf) Viewer](#debug-output-in-debug-printf-viewer)

## Install missing packs<br/>               ![](./images/image_1.png)
## Start Pack Installer<br/>                ![](./images/image_2.png)
## Install packs
   ![](./images/image_3.png)
## Install ARM::CMSIS-FreeRTOS
   ![](./images/image_4.png)
## Initial project build
   ![](./images/image_5.png)
## Start RTE configuration
   ![](./images/image_6.png)
## Select Software Components
   ![](./images/image_7.png)
## Add freertos_evr.c file to project
   ![](./images/image_8.png)<br/>![](./images/arrow.png)<br/>![](./images/image_9.png)
## Add include path
   ![](./images/image_10.png)<br/>![](./images/arrow.png)<br/>![](./images/image_11.png)<br/>![](./images/arrow.png)<br/>![](./images/image_12.png)
## Add include to FreeRTOSConfig.h file
   ![](./images/image_13.png)
## Update main.c file
   ![](./images/image_14.png)
## Add FreeRTOS.scvd file
   ![](./images/image_15.png)<br/>![](./images/arrow.png)<br/>![](./images/image_16.png)<br/>![](./images/arrow.png)<br/>![](./images/image_17.png)
## Start/Stop Debug session
   ![](./images/image_18.png)
## Open Event Recorder Window
   ![](./images/image_19.png)
## Start code execution
   ![](./images/image_20.png)
## FreeRTOS Events
   ![](./images/image_21.png)
## Update vMainUARTPrintString function in main.c file
   ![](./images/image_22.png)
## Re-target STDOUT to Event Recorder
   ![](./images/image_23.png)
## Edit Event Recorder Configuration
   ![](./images/image_24.png)<br/>![](./images/arrow.png)<br/>![](./images/image_25.png)
## Open Debug (printf) Viewer
   ![](./images/image_26.png)
## Debug Output in Debug (printf) Viewer
   ![](./images/image_27.png)
