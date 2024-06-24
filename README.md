# Mobile-controlled-home
main.c
Created on: Feb 1, 2024
 Author: HP
*/ #include "STD_TYPES .h" #include "BIT_MATH .h" #include "DIO_interface .h" #include "CLCD_interface .h" #include "UART_interface .h" #include "SSD_interface .h" #include <util/delay.h>

#define CLCD_DATA_PORT PORTA_REG #define CLCD_CONTROL_PORT PORTC_REG #define SSD_PORT PORTB_REG #define MOTOR_PORT PORTC_REG #define ROOM1_PORT PORTD_REG #define ROOM2_PORT PORTD_REG #define BUZZER_PORT PORTC_REG

u8 recievedData[20]; // to store Recieved Name u8 recievedChar; //TO recieve char from user u8 recievedPass[20]; // to store Recieved Passward u8 recievedNum; //to recieve number from user

typedef struct { char name[10]; u8 passward[10]; } checks;

checks data[10] = { {"ROBA", "1410"}, {"REHAM", "2412"}, {"HABIBA", "0106"}, {"RAFEEF", "410"}, {"AHMED", "1710"}, {"FATMA", "2510"}, {"RANA", "0907"}, {"HANAN", "1211"}, {"AMER", "2411"}, {"DINA", "2111"}};

u8 sizeString(u8 *string); u8 compare_strings(u8 *string1, u8 *string2, u32 length1, u32 length2); u8 check_name(u8 index); u8 check_password(u8 index);

int main() { // Data CLCD DIO_voidSetPortDir(CLCD_DATA_PORT, PORT_DIR_OUT); // 7 Segment DIO_voidSetPortDir(SSD_PORT, PORT_DIR_OUT); // Control CLCD DIO_voidSetPortDir(CLCD_CONTROL_PORT, PORT_DIR_OUT); // MOTOR DIO_voidSetPinDir(MOTOR_PORT,PIN3,PIN_DIR_OUT);//ONE DIRECTION DIO_voidSetPinDir(MOTOR_PORT,PIN4,PIN_DIR_OUT);//ONE DIRECTION

DIO_voidSetPinDir(MOTOR_PORT,PIN5,PIN_DIR_OUT);//OPPOSITE DIRECTION
DIO_voidSetPinDir(MOTOR_PORT,PIN6,PIN_DIR_OUT);//OPPOSITE DIRECTION

//ROOM1
DIO_voidSetPinDir(ROOM1_PORT,PIN7,PIN_DIR_OUT);

//ROOM2
DIO_voidSetPinDir(ROOM2_PORT,PIN5,PIN_DIR_OUT);

//RX
DIO_voidSetPinDir(PORTD_REG,PIN0,PIN_DIR_IN);

//TX
DIO_voidSetPinDir(PORTD_REG,PIN1,PIN_DIR_OUT);
//BUZZER
DIO_voidSetPinDir(BUZZER_PORT,PIN7,PIN_DIR_OUT);

SSD_t seven = {PORTB_REG, PORTB_REG, PIN7, COMMON_CATHODE};

UART_voidInit();
CLCD_voidInit();

CLCD_voidSendString("Hello To....");
UART_SendString("Hello To....");
UART_SendString("\r\n");
_delay_ms(1000);
CLCD_voidSendClearCommand();
CLCD_voidSendString("Mobile Controlled");
UART_SendString("Mobile Controlled Home");
UART_SendString("\r\n");
UART_SendString("\r\n");
CLCD_voidSendPos(1, 5);
CLCD_voidSendString("Home");
_delay_ms(2000);
CLCD_voidSendClearCommand();
u8 Counter = 0;
while (1)
{
	u8 i = 0;
	u8 w = 0;
	// Recieve Name and store it on array
	UART_SendString("ENTER YOUR NAME:");
	_delay_ms(100);
	UART_SendString("\r\n");
	CLCD_voidSendString("ENTER NAME:");

	while ((recievedChar = UART_u8Receive()) != '.')
	{
		recievedData[i] = recievedChar;
		i++;
	}
	recievedData[i] = '\0';
	//  UART_SendString(recievedData);
	UART_SendString("\r\n");
	//  _delay_ms(1000);
	CLCD_voidSendString(recievedData);

	CLCD_voidSendPos(1, 0);

	// Recieve Passward and store it on array
	CLCD_voidSendString("ENTER PASS:");
	UART_SendString("ENTER YOUR PASSWORD:");
	UART_SendString("\r\n");

	u8 l = 0;
	while ((recievedNum = UART_u8Receive()) != '.')
	{
		recievedPass[l] = recievedNum;
		l++;
	}
	recievedPass[l] = '\0';
	UART_SendString("\r\n");
	CLCD_voidSendString(recievedPass);

	_delay_ms(1000);

	CLCD_voidSendClearCommand();

	u8 flagg=0;
	for(u8 h=0;h<10;h++)
	{
		u8 tesstname=check_name(h);
		u8 tesstpass=check_password(h);
		if(tesstname==1 && tesstpass==1)
		{
			flagg=1;
			break;
		}
	}
	// check Name andPassward Recieved
	if (flagg==1)
	{

		CLCD_voidSendString("CORRECT DATA");


		UART_SendString("CORRECT DATA");
		UART_SendString("\r\n");
		_delay_ms(1000);
		CLCD_voidSendClearCommand();

		UART_SendString(" OPTIONS FOR YOUR HOME: ");
		UART_SendString("\r\n");
		CLCD_voidSendString("Welcome to Mobile");
		CLCD_voidSendPos(1, 1);
		CLCD_voidSendString("Controlled Home");
		_delay_ms(2000);
		CLCD_voidSendClearCommand();
		UART_SendString("1-ROOM1 ");
		UART_SendString("\r\n");
		UART_SendString("2-ROOM2  ");
		UART_SendString("\r\n");
		UART_SendString("3-OPEN DOOR  ");
		UART_SendString("\r\n");
		UART_SendString("4-CLOSE DOOR ");
		UART_SendString("\r\n");
		UART_SendString("5-CLOSE ROOM ");
		UART_SendString("\r\n");
		UART_SendString("6-LOGGING OUT  ");
		UART_SendString("\r\n");
		CLCD_voidSendString("ROOM1  PRESS 1");

		CLCD_voidSendPos(1, 0);
		CLCD_voidSendString("ROOM2  PRESS 2");
		_delay_ms(1000);

		CLCD_voidSendClearCommand();
		CLCD_voidSendString("  OPEN DOOR ");
		CLCD_voidSendPos(1, 0);
		CLCD_voidSendString("     PRESS 3");
		_delay_ms(1000);

		CLCD_voidSendClearCommand();
		CLCD_voidSendString("  CLOSE DOOR ");
		CLCD_voidSendPos(1, 0);
		CLCD_voidSendString("     PRESS 4");
		_delay_ms(1000);

		CLCD_voidSendClearCommand();
		CLCD_voidSendString("  CLOSE ROOM ");
		CLCD_voidSendPos(1, 0);
		CLCD_voidSendString("      PRESS 5");
		_delay_ms(2000);

		CLCD_voidSendClearCommand();
		CLCD_voidSendString("   LOGGING OUT");
		CLCD_voidSendPos(1, 0);
		CLCD_voidSendString("       PRESS 6");
		_delay_ms(1000);

		CLCD_voidSendClearCommand();

		while (UART_u8Receive() != '6')
		{
			u8 test = UART_u8Receive();
			switch (test)
			{
			case '1':
				UART_SendString("\r\n");
				DIO_voidSetPinVal(ROOM1_PORT, PIN7, 1);
				CLCD_voidSendClearCommand();
				UART_SendString("ROOM1 OPENED SUCESSFULLY");
				_delay_ms(50);
				CLCD_voidSendString("ROOM1 OPENED");
				CLCD_voidSendPos(1, 2);
				CLCD_voidSendString(" SUCESSFULLY");

				break;
			case '2':
				UART_SendString("\r\n");
				DIO_voidSetPinVal(ROOM2_PORT, PIN5, 1);
				CLCD_voidSendClearCommand();
				UART_SendString("ROOM2 OPENED SUCESSFULLY");
				_delay_ms(50);
				CLCD_voidSendString("ROOM2 OPENED");
				CLCD_voidSendPos(1, 2);
				CLCD_voidSendString(" SUCESSFULLY");

				break;
			case '3':
				UART_SendString("\r\n");
				DIO_voidSetPinVal(MOTOR_PORT, PIN5, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN6, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN3, 1);
				DIO_voidSetPinVal(MOTOR_PORT, PIN4, 1);
				_delay_ms(50);
				CLCD_voidSendClearCommand();
				UART_SendString("DOOR OPENED SUCESSFULLY");
				_delay_ms(50);
				CLCD_voidSendString("DOOR OPENED");
				CLCD_voidSendPos(1, 2);
				CLCD_voidSendString("SUCESSFULLY");

				DIO_voidSetPinVal(MOTOR_PORT, PIN5, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN6, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN3, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN4, 0);
				break;
			case '4':
				UART_SendString("\r\n");
				DIO_voidSetPinVal(MOTOR_PORT, PIN3, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN4, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN5, 1);
				DIO_voidSetPinVal(MOTOR_PORT, PIN6, 1);
				_delay_ms(50);
				CLCD_voidSendClearCommand();
				UART_SendString("DOOR CLOSED SUCESSFULLY");
				_delay_ms(50);
				CLCD_voidSendString("DOOR CLOSED");
				CLCD_voidSendPos(1, 2);
				CLCD_voidSendString("SUCESSFULLY");

				DIO_voidSetPinVal(MOTOR_PORT, PIN3, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN4, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN5, 0);
				DIO_voidSetPinVal(MOTOR_PORT, PIN6, 0);
				break;
			case '5':
				UART_SendString("\r\n");
				DIO_voidSetPinVal(ROOM1_PORT, PIN7, 0);
				DIO_voidSetPinVal(ROOM2_PORT, PIN5, 0);
				CLCD_voidSendClearCommand();
				UART_SendString("ROOM CLOSED SUCESSFULLY");
				_delay_ms(50);
				CLCD_voidSendString("ROOM CLOSED");
				CLCD_voidSendPos(1, 2);
				CLCD_voidSendString("SUCESSFULLY");

				break;
			}
		}
		CLCD_voidSendClearCommand();
		CLCD_voidSendString("  LOGGING OUT");
		_delay_ms(1000);
		UART_SendString("\r\n");
		UART_SendString("LOGGING OUT");
		UART_SendString("\r\n");
		CLCD_voidSendClearCommand();
		Counter=0;
	}
	else
	{
		CLCD_voidSendPos(0, 5);
		CLCD_voidSendString("Wrong");
		_delay_ms(1000);
		UART_SendString("Wrong Data");
		UART_SendString("\r\n");
		UART_SendString("Try Again");
		UART_SendString("\r\n");
		CLCD_voidSendClearCommand();
		if (Counter == 0)
		{
			CLCD_voidSendString("TWO MORE TRIES");
			_delay_ms(2000);
			UART_SendString("TWO MORE TRIES Remaining");
			UART_SendString("\r\n");

			Counter++;
			CLCD_voidSendClearCommand();
		}
		else if (Counter == 1)
		{
			CLCD_voidSendString("ONE MORE TRIE");
			_delay_ms(2000);
			UART_SendString("ONE MORE TRIE");
			UART_SendString("\r\n");
			Counter++;
			CLCD_voidSendClearCommand();
		}
		else if (Counter == 2)
		{
			CLCD_voidSendString("NO TRIES MORE");
			// BUZZER
			UART_SendString("NO TRIES MORE");
			UART_SendString("\r\n");


			CLCD_voidSendClearCommand();
			CLCD_voidSendString("Wait 10 Second ");

			_delay_ms(1000);
			UART_SendString("Wait 10 Second ");
			UART_SendString("\r\n");
			CLCD_voidSendClearCommand();
			u8 data = 9;
			u8 flag = 0;
			SSD_voidEnable(seven);
			while (flag != 1)
			{
				DIO_voidSetPinVal(BUZZER_PORT, PIN7, PIN_VAL_HIGH);
				_delay_ms(50);

				if (data == 0)
				{
					flag = 1;
				}

				CLCD_voidSendString("Loading.....");

				// CLCD_voidSendPos(0,14);
				if (data != 0)
				{
					CLCD_voidSendNum(data);

					SSD_voidSendNum(seven, data);
				}
				else
				{
					CLCD_voidSendNum(0);
					SSD_voidSendNum(seven, data);
					_delay_ms(1000);
				}

				_delay_ms(1000);
				CLCD_voidSendClearCommand();

				data--;
			}

			// SSD_voidDisable(seven);
			//_delay_ms(1000);
			DIO_voidSetPinVal(BUZZER_PORT, PIN7, PIN_VAL_LOW);

			CLCD_voidSendCommand(0b00000001);
			_delay_ms(50);
			Counter = 0;
		}
	}

	// Delete buffer
	for (int j = 0; j < 20; j++)
	{
		recievedData[j] = 0;
		recievedPass[j] = 0;
	}
	w = 0;
	_delay_ms(2000);
	CLCD_voidSendClearCommand();
}
return 0;
}

u8 sizeString(u8 *string) { u8 i = 0; while (string[i] != '\0') { i++; } return i; } u8 compare_strings(u8 *string1, u8 *string2, u32 length1, u32 length2) { if (length1 != length2) { return 0; } else { for (u32 i = 0; i < length1; i++) { if (string1[i] != string2[i]) { return 0; } } return 1; } } u8 check_password(u8 index) { //x is index of struct of entered username u8 test = compare_strings(recievedPass, data[index].passward,sizeString(recievedPass),sizeString(data[index].passward)); if (1 == test) { //CLCD_voidSendString("correct password"); //led on or open door return 1; } else { return 0; } } u8 check_name(u8 index) { //x is index of struct of entered username u8 test = compare_strings(recievedData, data[index].name,sizeString(recievedData),sizeString(data[index].name)); if (1 == test) { //CLCD_voidSendString("correct password"); //led on or open door return 1; } else { return 0; } }
