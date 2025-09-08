# Automatic-Railway-Gate-Control-System
This project improves safety at railway crossings.It uses IR sensors to detect the arrival and departure of trains.When a train is detected,the system automatically lowers the gate and turns On red light for vehicles.After the train passes,the gate opens and the green light is turned ON.
#include <avr/io.h>
#include <util/delay.h>

#define GATE_MOTOR PB0     // Gate motor control pin
#define RED_LIGHT  PB1     // Red signal light
#define GREEN_LIGHT PB2    // Green signal light

#define SENSOR_IN  PD0     // Train arrival sensor
#define SENSOR_OUT PD1     // Train departure sensor

int main(void) {
    DDRB |= (1<<GATE_MOTOR) | (1<<RED_LIGHT) | (1<<GREEN_LIGHT); // Outputs
    DDRD &= ~((1<<SENSOR_IN) | (1<<SENSOR_OUT));                 // Inputs
    
    PORTB |= (1<<GREEN_LIGHT);  // Initially gate open, green light ON
    
    while(1) {
        if(!(PIND & (1<<SENSOR_IN))) {   // Train approaching
            PORTB &= ~(1<<GREEN_LIGHT);  // Green OFF
            PORTB |= (1<<RED_LIGHT);     // Red ON
            PORTB |= (1<<GATE_MOTOR);    // Close gate (motor ON)
            _delay_ms(2000);             // Simulate gate closing
            PORTB &= ~(1<<GATE_MOTOR);   // Stop motor
        }
        
        if(!(PIND & (1<<SENSOR_OUT))) {  // Train leaving
            PORTB &= ~(1<<RED_LIGHT);    // Red OFF
            PORTB |= (1<<GATE_MOTOR);    // Open gate (motor ON)
            _delay_ms(2000);             // Simulate gate opening
            PORTB &= ~(1<<GATE_MOTOR);   // Stop motor
            PORTB |= (1<<GREEN_LIGHT);   // Green ON
        }
    }
}
