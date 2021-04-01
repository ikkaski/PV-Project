//PV-Project
#include <stdio.h>

int main() {

	// Get latitude from user and use to estimate irradiance (in U.S.)
	float latitude;
	
	// Different groups of appliances, based on wattage
	int first, second, third, fourth, fifth, sixth, seventh, eighth, ninth, tenth;
	
	// The total times (t for time I guess) that each appliance group will be running, multiplying this with the wattage will yield Watt hours (nearest half hour)
	float tfirst, tsecond, tthird, tfourth, tfifth, tsixth, tseventh, teighth, tninth, ttenth;

	// ask user for latitude
	printf("Enter your latitude: \n");

	// scanf() reads the formatted input and stores them
	scanf("%f", &latitude);

	// printf() displays the formatted output
	printf("Latidude: %f\n", latitude);
	printf("We will now ask some questions about your load requirements. \n");
	
	// Appliances with similar wattages can be grouped together. These are all around 1200W.
	printf("How many of these appliances will run off of this PV system: Fridge, Freezer, Electric Oven, Hair Dryer, Microwave, Toaster Oven, Electric Kettle, or Electric Space Heater? \n");
	
	scanf("%d", &first);
	
	printf("How many hours per day total (to the nearest half hour) will you be running the hair dryer, ovens, microwave, kettle, or space heaters (if applicable)? \n");
	scanf("%f", &tfirst);
	
	//Around 900W each
	printf("How many of these items will run off of this PV system: Blender, espresso/coffee machine, electric power tools, or vacuums? \n");
	scanf("%d", &second);
	
	printf("How many hours a day will these items be operating? \n");
	scanf("%f", &tsecond);
	
	//Around 200W each
	printf("How many of these items will run off of this PV system: Televisions, desktop computers, paper shredders, humudifiers, electric blankets, electric can openers, or box fans? \n");
	scanf("%d", &third);
	
	printf("How many hours per day will these items be operating? \n");
	scanf("%f", &tthird);
	
	printf("\n\n");
	
	//light bulbs
	printf("How many LED bulbs (40W equivalent) will be powered by the PV system? \n");
	scanf("%d", &fourth);
	
	printf("How many LED bulbs (60W equivalent) will be powered by the PV system? \n");
	scanf("%d", &fifth);
	
	printf("How many LED bulbs (75W equivalent) will be powered by the PV system? \n");
	scanf("%d", &sixth);
	
	printf("How many LED bulbs (100W equivalent) will be powered by the PV system? \n");
	scanf("%d", &seventh);
	
	printf("How many Incandescent or halogen bulbs will be powered by the PV system? \n");
	scanf("%d", &eighth);
	
	printf("What is the wattage of the incandescent or halogen bulbs used? \n");
	scanf("%d", &ninth);
	
	printf("How many hours per day will the light bulbs be on? Round to the nearest half hour. \n");
	scanf("%f", &tfourth);
	
	return 0;
}

