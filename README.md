# LCD-interfacing-using-PIC-Microcontroller
Interfacing a 16 pin LCD screen using PIC microcontroller to demonstrate its use in various applications like Bill Printers, number displayers etc. 
PIC18F4550 microcontroller is used for the project. MPLAB software and pickit 3 are used to burn the code into the microcontroller. 
PIC flashes the sentences "MPMC Project" "LCD_interfacing" "Presented by" "Giri_Adithya" with a specific time delay that was setup in the code. 


*code*
#include <pic18f4550.h>
#include "Configuration_header_file.h"
void MSdelay(unsigned int ); /*Generate delay in ms*/
void LCD_Init(); /*Initialize LCD*/
void LCD_Command(unsigned char ); /*Send command to LCD*/
void LCD_Char(unsigned char x); /*Send data to LCD*/
void LCD_String(const char *); /*Display data string on LCD*/
void LCD_String_xy(char, char , const char *);
void LCD_Clear(); /*Clear LCD Screen*/
int main(void)
{
 OSCCON = 0x72; /*Use internal oscillator and
 * set frequency to 8 MHz*/
LCD_Init(); /*Initialize LCD to 5*8 matrix i
LCD_String_xy(1,2,"MPMC Project");
 LCD_String_xy(1,1,"LCD_interfacing"); /*Display string on 1st
row, 5th location*/
LCD_String_xy(2,2,"Presented By");
LCD_String_xy(3,2,"Giri_Adithya");/*Display string on 2nd row,1st
location*/
while(1);
}
void LCD_Init()
{
 LCD_Port = 0; /*PORT as Output Port*/
 MSdelay(15); /*15ms,16x2 LCD Power on delay*/
 LCD_Command(0x02); /*send for initialization of LCD
 *for nibble (4-bit) mode */
 LCD_Command(0x28); /*use 2 line and
 *initialize 5*8 matrix in (4-bit mode)*/
 LCD_Command(0x01); /*clear display screen*/
 LCD_Command(0x0c); /*display on cursor off*/
 LCD_Command(0x06); /*increment cursor */
}
void LCD_Command(unsigned char cmd )
{
ldata = (ldata & 0x0f) |(0xF0 & cmd); /*Send higher nibble of
command first to PORT*/
RS = 0; /*Command Register is selected i.e.RS=0*/
EN = 1; /*High-to-low pulse on Enable pin to latch
data*/
NOP();
EN = 0;
MSdelay(1);
ldata = (ldata & 0x0f) | (cmd<<4); /*Send lower nibble of*/
EN = 1;
NOP();
EN = 0;
MSdelay(3);
}
void LCD_Char(unsigned char dat)
{
ldata = (ldata & 0x0f) | (0xF0 & dat); /*Send higher nibble of data first
to PORT*/
RS = 1; /*Data Register is selected*/
EN = 1; /*High-to-low pulse on Enable pin to latch
data*/
NOP();
EN = 0;
MSdelay(100);
ldata = (ldata & 0x0f) | (dat<<4); /*Send lower nibble of data
to PORT*/
EN = 1;
NOP();
EN = 0;
MSdelay(300);
}
void LCD_String(const char *msg)
{
while((*msg)!=0)
{
 LCD_Char(*msg);
 msg++;
 }
}
void LCD_String_xy(char row,char pos,const char *msg)
{
 char location=0;
 if(row<=1)
 {
 location=(0x80) | ((pos) & 0x0f); /*Print message on 1st row
and desired location*/
 LCD_Command(location);
 }
 else
 {
 location=(0xC0) | ((pos) & 0x0f); /*Print message on 2nd row
and desired location*/
 LCD_Command(location);
 }

 LCD_String(msg);
}
void LCD_Clear()
{
 LCD_Command(0x01); /*clear display screen*/
}
void MSdelay(unsigned int val)
{
unsigned int i,j;
for(i=0;i<=val;i++)
 for(j=0;j<81;j++);
}
