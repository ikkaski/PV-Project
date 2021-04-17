#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>

//determines user's U.S. state electricity price
float determine_state_elec_price(char *);
float get_irradiance(char *);

int main() {

// Different groups of appliances, based on wattage
int first, second, third, fourth, fifth, sixth, seventh, eighth, ninth, tenth, eleventh, twelvth;

// The total times (t for time I guess) that each appliance group will be running, multiplying this with the wattage will yield Watt hours (nearest half hour)
float tfirst, tsecond, tthird, tfourth, tfifth, tsixth, tseventh, teighth, tninth, ttenth;

//1200W
printf("How many of these appliances will run off of this PV system: Fridge, Freezer, Electric Oven, Hair Dryer, Microwave, Toaster Oven, Electric Kettle, or Electric Space Heater? \n");
scanf("%d", &first);

printf("How many hours per day total (to the nearest half hour) will you be running the hair dryer, ovens, microwave, kettle, or space heaters (if applicable)? \n");
scanf("%f", &tfirst);

//Around 900W each
printf("How many of these items will run off of this PV system: Blender, espresso/coffee machine, electric power tools, or vacuums? \n");
scanf("%d", &second);

printf("How many hours a day will these items be operating? \n"); scanf("%f", &tsecond);

//Around 200W each
printf("How many of these items will run off of this PV system: Televisions, desktop computers, paper shredders, humudifiers, electric blankets, electric can openers, or box fans? \n");
scanf("%d", &third);

printf("How many hours per day will these items be operating? \n");
scanf("%f", &tthird);

//18000W
printf("How many tank-less water heaters will run off of the PV system? \n"); scanf("%d", &tenth);

//4500
printf("How many normal electric water heaters (with tank) will run off of the PV system? \n"); scanf("%d", &eleventh);
printf("How many hours per day will the water heater(s) be operating? \n"); scanf("%f", &tfifth);

//1800W
printf("How many hours per day will the washer and dryer be operating? Enter 0 if these will not be connected to the PV system \n");
scanf("%f", &tsixth);
printf("\n\n");

//light bulbs
printf("How many LED bulbs (40W equivalent) will be powered by the PV system? \n"); scanf("%d", &fourth);

printf("How many LED bulbs (60W equivalent) will be powered by the PV system? \n"); scanf("%d", &fifth);

printf("How many LED bulbs (75W equivalent) will be powered by the PV system? \n"); scanf("%d", &sixth);

printf("How many LED bulbs (100W equivalent) will be powered by the PV system? \n"); scanf("%d", &seventh);

printf("How many Incandescent or halogen bulbs will be powered by the PV system? \n"); scanf("%d", &eighth);

printf("What is the wattage of the incandescent or halogen bulbs used? \n"); scanf("%d", &ninth);

printf("How many hours per day will the light bulbs be on? Round to the nearest half hour. \n"); scanf("%f", &tfourth);
printf("Total wattage of any other major appliances, if any: \n"); scanf("%d", &twelvth);
printf("How many hours per day will these appliances be running? Round to the nearest half hour. \n"); scanf("%f", &tseventh);

//-------------------------------------------------- Payback period code portion -------------------------------------------------------

/* ( System setup cost ($) ) Payback period = --------------------------------------------------------------------------------- ( State avg. electricity price, $/kWh ) ( Yearly system power demand, kWh/yr )

Determine system setup cost

Total potential power demand of the system will need to be determined by summing the user's appliance inputs
The system cost estimate can then be determined using estimates from: https://news.energysage.com/how-much-does-the-average-solar-panel-installation-cost-in-the-u-s/
Determine state avg. electricity price

Get U.S. state from user as two letter input, e.g. SD for South Dakota
pass the U.S. state into the determine_state_elec_price function to get the electricity price
Determine yearly system power demand

*/

//--------------------------------------------Determine system setup cost estimate

float system_size_kW = 0;

//potential power demand of the system in kWh
system_size_kW = (1200*first + 900*second + 200*third + 40*fourth + 60*fifth
                  + 75*sixth + 100*seventh + eighth*ninth + 18000*tenth + 4500*eleventh + twelvth)/1000;

//plotted size vs. setup cost data in Excel - $2810 per kw
float system_setup_cost = 2810*system_size_kW;

printf("System setup cost estimate: %f \n", system_setup_cost);

//----------------------------------------- Determine state avg. electricity price

char user_US_state[3];

printf("Enter the two letter abbreviation for the U.S. state in which you are installing the system (e.g. SD for South Dakota): "); scanf("%s", user_US_state);

//returns price in c/kWh
float state_elec_price = determine_state_elec_price(user_US_state);

//convert to $/kWh
state_elec_price = state_elec_price/100;

printf("Your state's average electricity price: %f \n", state_elec_price);

//------------------------------------------- Determine yearly system power demand

//wattage of all appliances * usage (hrs)
float daily_appliance_power_kWh = ((18000*tenth + 4500*eleventh)*tfifth + twelvth*seventh + 1800*tsixth + 1200*first*tfirst + 900*second*tsecond
                                   + 200*third*tthird + (40*fourth + 60*fifth + 75*sixth + 100*seventh + eighth*ninth)*tfourth )/1000;

//yearly power demand
float yearly_appliance_power_kWh = daily_appliance_power_kWh*365;

printf("System yearly power demand: %f kWh \n", yearly_appliance_power_kWh);

//------------------------------------------- Determine and print payback period (years)

float payback_period_yrs = system_setup_cost/(state_elec_price*yearly_appliance_power_kWh);

printf("Payback period estimate: %f years \n", payback_period_yrs);

//-------------------------------------------------- End of payback period code portion ---------------------------------------------------------//
//-------------------------------------------------- Start of Panel and Battery Sizing Portion --------------------------------------------------
float panel_efficiency = 0.15;
float available_power_day;
float available_power_year;
float battery_number;
float irradiance = get_irradiance(user_US_state);
available_power_day = irradiance*panel_efficiency;
available_power_year = available_power_day*365;
battery_number = daily_appliance_power_kWh / 1.2;
battery_number = round(battery_number) + 1;
system_size_kW = round(system_size_kW)+1;
float panel_area_total = yearly_appliance_power_kWh / available_power_year;
printf("You will need %f square meters of solar panels that are 15%% efficient or better. Always round up. \n", panel_area_total);
printf("If your panels do not have built in inverters, you will need a single inverter rated for %f kW or more. \n",system_size_kW);
printf("If you plan to use battery backup, you can use %f 24V 100Ah batteries. This is with a 50%% depth of discharge. \n", battery_number);

//-----------------------------------------------------End of panel and battery sizing portion ---------------------------------------------------
return 0;

}

/* determines the avg electricity price to be used in payback period calculation, based on the user-entered U.S. state (the state's two letter abbreviation)

The average electricity prices in this file are taken from: https://www.eia.gov/electricity/state/ */
float determine_state_elec_price(char * user_US_state) {

float state_elec_price;     //state electricity price returned from function

if(strcmp(user_US_state, "AL") == 0) {state_elec_price = 9.83; return state_elec_price;}    //strcmp returns 0 if strings are equal
else if(strcmp(user_US_state, "AK") == 0) {state_elec_price = 20.22; return state_elec_price;}
else if(strcmp(user_US_state, "AZ") == 0) {state_elec_price = 10.52; return state_elec_price;}
else if(strcmp(user_US_state, "AR") == 0) {state_elec_price = 8.22; return state_elec_price;}
else if(strcmp(user_US_state, "CA") == 0) {state_elec_price = 16.89; return state_elec_price;}
else if(strcmp(user_US_state, "CO") == 0) {state_elec_price = 10.17; return state_elec_price;}
else if(strcmp(user_US_state, "CT") == 0) {state_elec_price = 18.66; return state_elec_price;}
else if(strcmp(user_US_state, "DE") == 0) {state_elec_price = 10.52; return state_elec_price;}
else if(strcmp(user_US_state, "DC") == 0) {state_elec_price = 12.27; return state_elec_price;}
else if(strcmp(user_US_state, "FL") == 0) {state_elec_price = 10.44; return state_elec_price;}
else if(strcmp(user_US_state, "GA") == 0) {state_elec_price = 9.86; return state_elec_price;}
else if(strcmp(user_US_state, "HI") == 0) {state_elec_price = 28.72; return state_elec_price;}
else if(strcmp(user_US_state, "ID") == 0) {state_elec_price = 7.89; return state_elec_price;}
else if(strcmp(user_US_state, "IL") == 0) {state_elec_price = 9.56; return state_elec_price;}
else if(strcmp(user_US_state, "IN") == 0) {state_elec_price = 9.91; return state_elec_price;}
else if(strcmp(user_US_state, "IA") == 0) {state_elec_price = 9.08; return state_elec_price;}
else if(strcmp(user_US_state, "KS") == 0) {state_elec_price = 10.26; return state_elec_price;}
else if(strcmp(user_US_state, "KY") == 0) {state_elec_price = 8.61; return state_elec_price;}
else if(strcmp(user_US_state, "LA") == 0) {state_elec_price = 7.71; return state_elec_price;}
else if(strcmp(user_US_state, "ME") == 0) {state_elec_price = 14.04; return state_elec_price;}
else if(strcmp(user_US_state, "MT") == 0) {state_elec_price = 11.24; return state_elec_price;}
else if(strcmp(user_US_state, "NE") == 0) {state_elec_price = 18.40; return state_elec_price;}
else if(strcmp(user_US_state, "NV") == 0) {state_elec_price = 11.56; return state_elec_price;}
else if(strcmp(user_US_state, "NH") == 0) {state_elec_price = 10.33; return state_elec_price;}
else if(strcmp(user_US_state, "NJ") == 0) {state_elec_price = 9.28; return state_elec_price;}
else if(strcmp(user_US_state, "NM") == 0) {state_elec_price = 9.68; return state_elec_price;}
else if(strcmp(user_US_state, "NY") == 0) {state_elec_price = 9.02; return state_elec_price;}
else if(strcmp(user_US_state, "NC") == 0) {state_elec_price = 9.08; return state_elec_price;}
else if(strcmp(user_US_state, "ND") == 0) {state_elec_price = 8.78; return state_elec_price;}
else if(strcmp(user_US_state, "OH") == 0) {state_elec_price = 17.15; return state_elec_price;}
else if(strcmp(user_US_state, "OK") == 0) {state_elec_price = 13.42; return state_elec_price;}
else if(strcmp(user_US_state, "OR") == 0) {state_elec_price = 8.99; return state_elec_price;}
else if(strcmp(user_US_state, "MD") == 0) {state_elec_price = 14.34; return state_elec_price;}
else if(strcmp(user_US_state, "MA") == 0) {state_elec_price = 9.45; return state_elec_price;}
else if(strcmp(user_US_state, "MI") == 0) {state_elec_price = 8.85; return state_elec_price;}
else if(strcmp(user_US_state, "MN") == 0) {state_elec_price = 9.58; return state_elec_price;}
else if(strcmp(user_US_state, "MS") == 0) {state_elec_price = 7.86; return state_elec_price;}
else if(strcmp(user_US_state, "MO") == 0) {state_elec_price = 8.81; return state_elec_price;}
else if(strcmp(user_US_state, "PA") == 0) {state_elec_price = 9.81; return state_elec_price;}
else if(strcmp(user_US_state, "RI") == 0) {state_elec_price = 18.49; return state_elec_price;}
else if(strcmp(user_US_state, "SC") == 0) {state_elec_price = 10.02; return state_elec_price;}
else if(strcmp(user_US_state, "SD") == 0) {state_elec_price = 9.96; return state_elec_price;}
else if(strcmp(user_US_state, "TN") == 0) {state_elec_price = 9.69; return state_elec_price;}
else if(strcmp(user_US_state, "TX") == 0) {state_elec_price = 8.60; return state_elec_price;}
else if(strcmp(user_US_state, "UT") == 0) {state_elec_price = 8.24; return state_elec_price;}
else if(strcmp(user_US_state, "VT") == 0) {state_elec_price = 15.36; return state_elec_price;}
else if(strcmp(user_US_state, "VA") == 0) {state_elec_price = 9.52; return state_elec_price;}
else if(strcmp(user_US_state, "WA") == 0) {state_elec_price = 8.04; return state_elec_price;}
else if(strcmp(user_US_state, "WV") == 0) {state_elec_price = 8.49; return state_elec_price;}
else if(strcmp(user_US_state, "WI") == 0) {state_elec_price = 10.66; return state_elec_price;}
else if(strcmp(user_US_state, "WY") == 0) {state_elec_price = 8.10; return state_elec_price;}
else {state_elec_price = 0; return state_elec_price;}
}

//------------------------------------------------------------------------------------------------------------------------
//determine irradiance based on state (kWh/m^2/day)
float get_irradiance(char * user_US_state) {

float irradiance;     //irradiance in kWh/m^2/day returned from function

if(strcmp(user_US_state, "AL") == 0) {irradiance = 4.4; return irradiance;}    //strcmp returns 0 if strings are equal
else if(strcmp(user_US_state, "AK") == 0) {irradiance = 3.5; return irradiance;}
else if(strcmp(user_US_state, "AZ") == 0) {irradiance = 5.9; return irradiance;}
else if(strcmp(user_US_state, "AR") == 0) {irradiance = 4.4; return irradiance;}
else if(strcmp(user_US_state, "CA") == 0) {irradiance = 5.0; return irradiance;}
else if(strcmp(user_US_state, "CO") == 0) {irradiance = 5.0; return irradiance;}
else if(strcmp(user_US_state, "CT") == 0) {irradiance = 4.0; return irradiance;}
else if(strcmp(user_US_state, "DE") == 0) {irradiance = 4.0; return irradiance;}
else if(strcmp(user_US_state, "DC") == 0) {irradiance = 4.0; return irradiance;}
else if(strcmp(user_US_state, "FL") == 0) {irradiance = 5.2; return irradiance;}
else if(strcmp(user_US_state, "GA") == 0) {irradiance = 4.4; return irradiance;}
else if(strcmp(user_US_state, "HI") == 0) {irradiance = 6.0; return irradiance;}
else if(strcmp(user_US_state, "ID") == 0) {irradiance = 3.5; return irradiance;}
else if(strcmp(user_US_state, "IL") == 0) {irradiance = 4.0; return irradiance;}
else if(strcmp(user_US_state, "IN") == 0) {irradiance = 3.7; return irradiance;}
else if(strcmp(user_US_state, "IA") == 0) {irradiance = 3.7; return irradiance;}
else if(strcmp(user_US_state, "KS") == 0) {irradiance = 4.5; return irradiance;}
else if(strcmp(user_US_state, "KY") == 0) {irradiance = 4.0; return irradiance;}
else if(strcmp(user_US_state, "LA") == 0) {irradiance = 4.4; return irradiance;}
else if(strcmp(user_US_state, "ME") == 0) {irradiance = 3.2; return irradiance;}
else if(strcmp(user_US_state, "MT") == 0) {irradiance = 3.5; return irradiance;}
else if(strcmp(user_US_state, "NE") == 0) {irradiance = 4.0; return irradiance;}
else if(strcmp(user_US_state, "NV") == 0) {irradiance = 4.9; return irradiance;}
else if(strcmp(user_US_state, "NH") == 0) {irradiance = 3.5; return irradiance;}
else if(strcmp(user_US_state, "NJ") == 0) {irradiance = 3.5; return irradiance;}
else if(strcmp(user_US_state, "NM") == 0) {irradiance = 6.4; return irradiance;}
else if(strcmp(user_US_state, "NY") == 0) {irradiance = 3.4; return irradiance;}
else if(strcmp(user_US_state, "NC") == 0) {irradiance = 4.4; return irradiance;}
else if(strcmp(user_US_state, "ND") == 0) {irradiance = 3.2; return irradiance;}
else if(strcmp(user_US_state, "OH") == 0) {irradiance = 3.6; return irradiance;}
else if(strcmp(user_US_state, "OK") == 0) {irradiance = 4.5; return irradiance;}
else if(strcmp(user_US_state, "OR") == 0) {irradiance = 3.6; return irradiance;}
else if(strcmp(user_US_state, "MD") == 0) {irradiance = 3.6; return irradiance;}
else if(strcmp(user_US_state, "MA") == 0) {irradiance = 3.2; return irradiance;}
else if(strcmp(user_US_state, "MI") == 0) {irradiance = 3.3; return irradiance;}
else if(strcmp(user_US_state, "MN") == 0) {irradiance = 3.3; return irradiance;}
else if(strcmp(user_US_state, "MS") == 0) {irradiance = 4.4; return irradiance;}
else if(strcmp(user_US_state, "MO") == 0) {irradiance = 4.0; return irradiance;}
else if(strcmp(user_US_state, "PA") == 0) {irradiance = 3.6; return irradiance;}
else if(strcmp(user_US_state, "RI") == 0) {irradiance = 3.3; return irradiance;}
else if(strcmp(user_US_state, "SC") == 0) {irradiance = 4.6; return irradiance;}
else if(strcmp(user_US_state, "SD") == 0) {irradiance = 3.9; return irradiance;}
else if(strcmp(user_US_state, "TN") == 0) {irradiance = 4.0; return irradiance;}
else if(strcmp(user_US_state, "TX") == 0) {irradiance = 5.0; return irradiance;}
else if(strcmp(user_US_state, "UT") == 0) {irradiance = 4.6; return irradiance;}
else if(strcmp(user_US_state, "VT") == 0) {irradiance = 3.2; return irradiance;}
else if(strcmp(user_US_state, "VA") == 0) {irradiance = 4.0; return irradiance;}
else if(strcmp(user_US_state, "WA") == 0) {irradiance = 3.5; return irradiance;}
else if(strcmp(user_US_state, "WV") == 0) {irradiance = 3.8; return irradiance;}
else if(strcmp(user_US_state, "WI") == 0) {irradiance = 3.3; return irradiance;}
else if(strcmp(user_US_state, "WY") == 0) {irradiance = 3.9; return irradiance;}
else {irradiance = 0; return irradiance;}
}

