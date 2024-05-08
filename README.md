# KEYPAD-AND-LCD-DISPLAY-INTERFACE
#include <LPC17xx.H>
#include "GLCD.h"

#define __FI 1

unsigned char key,key_val;

void delay(unsigned int x)				/* Delay routine */
{
 for(;x>0;x--);
}

void display(unsigned int i)
{		
	unsigned char a;
	a = (i/0x10) & 0x0f;
	{
		if(a<=9)
		a = a + 0x30;
		else
		a = a + 0x37;

  	GLCD_DisplayChar(8,16,__FI,a);
  }
		

	a = i%0x10;
	{
			if(a<=9)
			a = a + 0x30;
			else
			a = a + 0x37;

  	GLCD_DisplayChar(8,17,__FI,a);
  }
}	

unsigned char getkey()				   				    	/* Key Scan function */
 {
  unsigned long i,s,code,j;
  code = 0;
	for(i=0x00008000; i<=0x00400000; i<<=1)			/* Check for 8 Scan lines */
	{
		 	LPC_GPIO0->FIOPIN =  i;	                /* Make 1 scan line high */
 		  delay(32000);	
	  	s= LPC_GPIO0->FIOPIN & 0x03800000;
		  s = s >> 23;
	  	if(s>0)									       	/* Read the scanned line */
	  	{
	    	for(j=0;j<=2;j++)						  /* Check for 3 keys */
			  {
		  		s>>=1;
		  		if(s==0)
				  	 return(code+j);					/* If key Pressed return its code */
		   	}
		  }	
    code += 3;			
  }
 }
 
 
main()
{  
	LPC_SC->PCONP        |=   (1<<15);
	LPC_PINCON->PINMODE1 |=    0x000FC000;        /* Configure Pull-down Resistor for P0.23,P0.24,P0.25 */
	LPC_GPIO0->FIODIR    |=    0x007F8000;        /* Make portA as input and portC as output            */
	
	#ifdef __USE_LCD
  GLCD_Init();                               /* Initialize graphical LCD      */

  GLCD_Clear(White);                        /* Clear graphical LCD display   */
  GLCD_SetBackColor(Blue);
  GLCD_SetTextColor(White);
  GLCD_DisplayString(0, 0, __FI, "        ESA         ");
  GLCD_DisplayString(1, 0, __FI, "     Bangalore      ");
  GLCD_DisplayString(2, 0, __FI, "  www.esaindia.com  ");
  GLCD_SetBackColor(White);
  GLCD_SetTextColor(Blue);
  GLCD_DisplayString(5, 0, __FI, "   Calc Keyboard   ");
  GLCD_DisplayString(8, 0,__FI,  "   keypressed =    ");

  #endif
   
  while(1)						   
   {		
    key = getkey();	
		 switch(key)
		 {
			 case 0x00: key_val = 0x00;
		              break;
			 case 0x01: key_val = 0x08;
			            break;
			 case 0x02: key_val = 0x10;
			 		        break;
			 case 0x03: key_val = 0x01;
			            break;
			 case 0x04: key_val = 0x09;
			            break;
			 case 0x05: key_val = 0x11;
				          break;
			 case 0x06: key_val = 0x02;
			            break;
			 case 0x07: key_val = 0x0A;
			            break;
			 case 0x08: key_val = 0x12;
			 	          break;
			 case 0x09: key_val = 0x03;
		              break;
			 case 0x0A: key_val = 0x0B;
			            break;
			 case 0x0B: key_val = 0x13;
			            break; 
		 	 case 0x0C: key_val = 0x04;
			            break;
			 case 0x0D: key_val = 0x0C;
		              break;
			 case 0x0E: key_val = 0x14;
			            break;
			 case 0x0F: key_val = 0x05;
			            break;
			 case 0x10: key_val = 0x0D;
			            break;
			 case 0x11: key_val = 0x15;
			 	          break;
			 case 0x12: key_val = 0x06;
			            break;
			 case 0x13: key_val = 0x0E;
			 	         break;
			 case 0x14: key_val = 0x16;
			            break;
			 case 0x15: key_val = 0x07;
			 	         break;
       case 0x16: key_val = 0x0F;
			            break;
			 case 0x17: key_val = 0x17;
			 	         break;								 
		 }
	   display(key_val);	 
   }							   
} 							

