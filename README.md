# ardunio-assignment3
#include <avr/io.h>
#include <avr/interrupt.h>

#define lm35Pin A0      // LM35 temperature sensor pin
#define ledPin 13       // Onboard LED pin

volatile uint8_t ledState = 0;

void setup() {
  pinMode(ledPin, OUTPUT);

  // Configure Timer1 for interrupt every 500 milliseconds
  TCCR1A = 0;
  TCCR1B = 0;
  TCNT1 = 0;
  OCR1A = 31249;  // 16MHz / (prescaler * desired frequency) - 1
  TCCR1B |= (1 << WGM12);  // CTC mode
  TCCR1B |= (1 << CS12) | (1 << CS10);  // 1024 prescaler
  TIMSK1 |= (1 << OCIE1A);  // Enable Timer1 Compare A interrupt

  sei();  // Enable global interrupts
}

void loop() {
  int temperature = readTemperature();

  if (temperature < 30) {
    // Temperature below 30 degrees Celsius, LED state is managed by the interrupt
  } else {
    // Temperature above 30 degrees Celsius, blink every 500 milliseconds
    ledState = 1;  // Set the initial state to on
  }
}
ISR(TIMER1_COMPA_vect) {
 
  if (ledState) {
    PORTB ^= (1 << PB5);  
    ledState = 0;  
  }
}
int readTemperature() {
  return 35;
}

