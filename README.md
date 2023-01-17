# dynamic ess - Tariff based ESS controller

### Disclaimer

Work in progres. 

### Purpose

The objective of this project is to create a new feature for Victron Energy that takes into consideration the fluctuating rates of energy tariffs when determining whether to store energy or return it to the grid. These dynamic tariffs are currently offered by various energy providers, such as Easy Energy, ANWB, TIBR, and others. They are typically announced one day prior to taking effect, which is known as the day-ahead pricing model. The main output of this feature will be the control of the grid setpoint. The ultimate goal is to integrate this feature into VRM, and this Node-RED implementation is intended for testing purposes.

### Changes 17-01-22
* Added static threshold
* More stability
* Added, retrieve prices button
* Added, refresh button


### Prerequisites
* Venus OS Large (latest release, not latest candidate)
* ESS setup configured
* ESS mode set to: Optimized (with or without Batterylife)

### Install
1. Click on the `Menu` button (1)
2. Click on the `Manage Palette` option (2)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_1.png" width=30%>
</p>

3. Select the Install tab (3)
4. Search for `node-red-dashboard` in the search bar (4)
5. Click the Install button (5)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_2.png" width=40%>
</p>

6. Open the menu again
7. Click import (6)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_3.png" width=30%>
</p>

8. Paste the JSON file `https://raw.githubusercontent.com/tfranssen/dynamic-ess-NodeRED-flow/main/flow.js` in the textbox (7)
9. Click Import button (8)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_4.png" width=40% height=40%>
</p>

10. Click the UI:Dynamic ESS flow tab (9)
11. Deploy the flow (10)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_5.png" width=40% height=40%>
</p>

11. Double click the `Retrieve prices from ENTSO-e` node (11 and 12)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_6.png" width=40% height=40%>
</p>

13. Fill in the ENTSO-e key (13)
14. Click Done

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_7.png" width=40% height=40%>
</p>

Unfortunately we have to do a small work-around to deal with a Node-RED quirk.

13. Search the `MultiPlus-II 48/3000/35-32 | Disable charge` node (14)
14. Cut this node so it disappears using `ctrl-x` or `cmd-x`
15. Past the node to the same location using `crtl-v` or `cmd-v`
16. Connect the wire again
17. Deploy the flow again

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_8.png" width=40% height=40%>
</p>

18. Click the `Run first time manually` node (15)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_9.png" width=40% height=40%>
</p>

19. Go to `<<your-node-red-ip>>:1883/ui` 

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_10.png" width=40% height=40%>
</p>

### Features

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_11.png" width=40% height=40%>
</p>

1. Sell threshold setting determines at what point the grid setpoint will be set to a positive value and the battery will begin discharging and returning energy to the grid when the sell switch (4) is enabled. The default value for this setting is -3000 watts.
2. Buy threshold works in a similar manner as the sell threshold, with the key difference being that when the current price is equal to or lower than this value, the battery will begin charging. The default value for this setting is 3000 watts.
3. Buy switch enables the battery to charge and buy energy from the grid when the current price is low.
4. Sell switch determines if the battery will discharge and sell energy to the grid when the current price is high.
5. Disable charge switch enables a feature that prevents PV power from flowing into the battery.
6. Charge/Buy button, when clicked, causes the battery to charge with maximum power and power is purchased from the grid.
7. Discharge/Sell button, when clicked, causes the battery to discharge with maximum power and power is sold to the grid.
8. Auto button, when clicked, will set the grid setpoint to its default value.
9. Hide Tomorrow Prices switch allows you to toggle on/off the visibility of the prices for the next day. When turned on, the prices for tomorrow will be hidden.

### To do
* Gather pricing information from various energy providers such as:
    * ENTSOE API (completed)
    * ANWB
    * Easy Energy
    * TIBR
* Create a visual representation of the prices via a chart (completed)
* Develop different charging strategies:
    * Charge the battery when prices are lower than average (completed)
    * Charge when prices are lower than average, and discharge when prices are higher than average (completed)
    * Always charge during the X lowest tariff hours. (For this, prices will be sorted in ascending order, and the first X hours will be used for charging)
    * Implement the above scenarios with PV forecast. The SoC will be kept lower in the morning, providing capacity for PV charging.
* Add State of Charge limits for charging and discharging.
* Implement logging feature
* Set up a scheduler (completed)
* Incorporate PV forecast.

### Run 

* Click deploy
* Go to `<<your-node-red-ip>>:1883/ui`

### ENTSO-e API Access
You need an ENTSO-e Restful API key if you want to collect the data from ENTSO-e. To request this API key, register on the Transparency Platform `https://transparency.entsoe.eu/` and send an email to `transparency@entsoe.eu` with `Restful API access` in the subject line. Indicate the email address you entered during registration in the email body.

### Schedule
* Get prices is scheduled every day at 00:00:00.
* Prices for next day are retreived at 15:00:00.
* The ESS controller is scheduled every 5 minutes. If the charge requirement did change an MQTT message will be published. Otherwise nothing will happen.
