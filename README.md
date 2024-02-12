//*****************************************************************************
// Universidad del Valle de Guatemala
// IE2023: Programación de microcontroladores
// Autor: James Ramírez
// Proyecto: Laboratorio 2.asm
// Descripción: Código de laboratorio 2
// Hardware: ATMega328p
// Creado: 24/01/2024
//*****************************************************************************

// Encabezado
.include "M328PDEF.inc"
.cseg
.org 0x00
	JMP MAIN_LOOP

//*****************************************************************************
// Configuración de la Pila
//*****************************************************************************
LDI R16, LOW(RAMEND)
OUT SPL, R16
LDI R17, HIGH(RAMEND)
OUT SPH, R17

//*****************************************************************************
// Configuración MCU
//*****************************************************************************
SETUPC1:
   // Configuración de pines
   LDI R16, 0b11111111  ; Configura los pines PD como salida
   OUT DDRD, R16	


   // Habilita las resistencias de pull-up en los pines de entrada
   LDI R22, 0b00000011
   OUT PORTB, R22
   CBI DDRB, 0b00000000

   // Inicializa el contador en 0
   LDI R18, 0


MAIN_LOOP:

   SBIC PINB, PB0		// Comprueba el estado del pulsador de incremento.
   CALL NUM1			// Si el push de incremento está activo, salta a la configuración de incremento del contador.

   SBIC PINB, PB1		// Comprueba el estado del pulsador de decremento.
   CALL NUM2			// Si el push de decremento está activo, salta a la confituración de decremento del contador.

   RJMP MAIN_LOOP		// Si detecta que ninguno de los pulsadores está activo, reinicia el proceso hasta detectar un cambio.


//Esta subrutina sirve para el antirrebote del pushbutton
NUM1:
	LDI R17, 5  //Sirve para hacer un delay 
	delay:
		DEC R17
		BRNE delay
	SBIC PINB, PB0		//Analiza que el puerto no siga teniendo entrada de información
	RJMP NUM1			
	INC R18
	OUT PORTB, R18
	CALL MAIN
	RET

//Esta subrutina sirve para el antirrebote del pushbutton
// y tambien sirve para decrementar el valor de R18 

		NUM2:
			LDI R17, 5  //Sirve para hacer un delay
			delay2:
				DEC R17
				BRNE delay2
			SBIS PINB, PB1	//Analiza que el puerto no siga teniendo entrada de información
			RJMP NUM2			
			DEC R18
			OUT PORTB, R18
			CALL MAIN
			RET


//*****************************************************************************
	//TABLA DE DISPLAY
//*****************************************************************************
	TABLA7SEG: .DB	0x3F, 0x06, 0x5B, 0x4F, 0x66, 0x6D, 0x7D, 0x07, 0x7F, 0x67

//**************************************************************************
   	// Configuración para el display
//*****************************************************************************
MAIN:

	LDI ZH, HIGH(TABLA7SEG << 1)
	LDI ZL, LOW(TABLA7SEG << 1)
	ADD ZL, R18 
	LPM R18, Z  
	RET
