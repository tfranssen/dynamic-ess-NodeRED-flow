# dynamic ess - Tariff based ESS controller

### Disclaimer

Work in progres. 

### Purpose

The goal of this project is to develop a feature for Victron Energy to take into account the dynamic tariff prices in the decision to store energy or to return energy to the grid. These dynamic tariff prices are nowadays offered by various energy providers (Easy Energy, ANWB, TIBR, and others) and are always announced a day in advance (also known as the day-ahead pricing model). The feature will control the grid setpoint as its main output. The goal is to implement this feature in VRM. This Node-RED implementation is for testing purposes. 

### Install
1. Click on Menu (1)
2. Add `node-red-dashboard` to palette (2)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_1.png" width=40% height=40%>
</p>

3. Select the install tab (3)
4. Search for `node-red-dashboard` (4)
5. Click install button (5)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_2.png" width=40% height=40%>
</p>

6. Open the menu again
7. Click import (6)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_3.png" width=40% height=40%>
</p>

8. Paste the JSON file `https://raw.githubusercontent.com/tfranssen/dynamic-ess-NodeRED-flow/main/flow.js` in the textbox (7)
9. Click Import button (8)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_4.png" width=40% height=40%>
</p>

10. Click the UI:Dynamic ESS flow tab (9)
11. Deploy the flow (10)

<p align="center">
    <img src="https://github.com/tfranssen/dynamic-ess-NodeRED-flow/raw/main/img/install_t.png" width=40% height=40%>
</p>





### Config

1. Enter ENTSO-e key in both ENTSO-e functions
2. Run `first time manually` node

### To do
* Retrieve prices from multiple energy providers:
  * ~~ENTSOE API~~ (done)  
  * ANWB
  * Easy Energy
  * TIBR
* ~~Plot chart~~ (done)
* ~~ADD MQTT functionality through VRM~~ (done)
* Implement multiple charge scenarios 
  * ~~Simple charge when prices are X% lower then average~~ (done)
  * ~~Simple charge when prices are X% lower then average, discharge when prices are X% higher then average~~ (done)
  * Always charge in X lowest tariff hours. (In this cases prices will be sorted in ascending order, first X hours will be used for charging)
  * Above scenarios including PV forecast. SoC will be lower in the morning so there is capacity left for PV charging. 
* Add SoC limits for charge and discharge
* Implement logging
* ~~Implement scheduler~~ (done)
* Implement PV forecast





### Run 

* Click deploy
* Go to `<<your-node-red-ip>>:1883/ui`


### Settings
* `lowChargeLimit` this is the threshold used to start charging. Default = 0.8, charging starts in this case 20% below daily average
* `highThreshold` = Constant to set the high threshold for selling. Default =1.2. Only used in Mode 2


### ENTSO-e API Access
You need an ENTSO-e Restful API key if you want to collect the data from ENTSO-e. To request this API key, register on the Transparency Platform `https://transparency.entsoe.eu/` and send an email to `transparency@entsoe.eu` with `Restful API access` in the subject line. Indicate the email address you entered during registration in the email body.

### Schedule
* Get prices is scheduled every day at 00:00:00.
* The ESS controller is scheduled every 5 minutes. If the charge requirement did change an MQTT message will be published. Otherwise nothing will happen.
