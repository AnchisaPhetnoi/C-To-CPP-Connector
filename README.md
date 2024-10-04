#  การเรียกใช้ library ในภาษา C++ จากไฟล์ภาษา C

## กล่าวนำ
บ่อยครั้งที่ Library หรือ component ต่าง ๆ ถูกเขียนไว้ในภาษา C++ เนื่องจากมีความสะดวกในการเขียนโปรแกรมเชิงวัตถุ แต่มีโปรแกรมจำนวนไม่น้อยที่ต้องเขียนในภาษา C และมีความจำเป็นต้องเรียกใช้ library หรือ component เหล่านั้น

ภาษา C++ มีจุดกำเนิดภายหลังภาษา C จึงรองรับการเข้ากันได้กับภาษา C ในตัว (backward compatability) แต่ภาษา C นั้นเกิดขึ้นมาก่อน และไม่รู้จักภาษา C++

การเรียกใช้ code ในภาษา C จากภาษา C++ จึงไม่มีปัญหาใดๆ สามารถเรียกใช้ได้ทันที แต่การเรียก code ในภาษา C++ จากภาษา C จะมีความซับซ้อนมากกว่า เรื่องจากภาษา  C++ รองรับการใช้ชื่อเดียวสำหรับหลายการปฎิบัติการ (Polymorphism) ซึ่งไม่มีกลไกการเรียกฟังก์ชันชนิด polymorphism ในภาษา C กล่าวโดยสรุปคือ ภาษา C มีปัญหาในการเรียกชื่อของฟังก์ชันที่ซ้ำกัน (overloading) ในภาษา C++ นั่นเอง

เพื่อแก้ปัญหาดังกล่าว เราต้องกำหนดสภาพแวดล้อมในการเรียกใช้งานฟังก์ชันระหว่างภาษา และแยกขอบเขตของชื่อเรียกให้เหมาะสม ก็จะสามารถเรียกใช้ code ในภาษา C++ จากภาษา C ได้ตามต้องการ

### การระบุขอบเขตระหว่างภาษา C และ C++

ในภาษา C++ มีคีย์เวิร์ดสำหรับกำหนดของเขตของภาษาคือ 

```cpp
extern "C" 
{


}
```

ภายในบล็อกของเครื่องหมาย `{ }` จะเป็นพื้นที่ของภาษา C++ และภายนอกเครื่องหมายปีกกา จะเป็นพื้นที่ของภาษา C

### ตัวอย่าง การใช้ component `LED` ซึ่งถูกเขียนในภาษา C++ ในโปรเจค ESP32 ที่เขียนด้วยภาษา C

เพื่อความสะดวกในการเรียกใช้ LED component ที่เขียนในภาษา C++  นั้น เราจะสร้างไฟล์ lec_c_connector.cpp ขึ้นมา โดยในบล็อก `extern "C" {}` สามารถเขียน code ที่เป็นภาษา C++ โดยสมบูรณ์ตามหลักการของ OOP ทุกประการ ในขณะเดียวกัน ไฟล์ดังกล่าวจะสามารถเขียน code ที่เป็นภาษา C เพื่อให้ฟังก์ชันใน main.c (เช่น `app_main()`) สามารถเรียกใช้งานได้

``` mermaid
graph LR
    A[app_main : C] ---> B[LED_c_connector : C++]
    B ---> C[LED Component : C++]
```    

### ตัวอย่างไฟล์  led_c_connector.h

```cpp
#ifndef LED_C_CONNECTOR_H
#define LED_C_CONNECTOR_H

#ifdef __cplusplus
extern "C"
{
#endif

    void LED_ON();
    void LED_OFF();

#ifdef __cplusplus
}
#endif
#endif
```


### ตัวอย่างไฟล์  led_c_connector.cpp

```cpp
#include "led_c_connector.h"
#include "LED.h"

#ifdef __cplusplus   // ตรวจสอบว่าไฟล์เป็นชนิด CPP หรือไม่ ถ้าใช่ ให้ล้อม code ที่จะเขียนในภาษา C++ ไว้ในบล็อก นี้
extern "C" {   
#endif

// ภายในบล็อกนี้ สามารถเขียนเป็น CPP ได้โดยสมบูรณ์

LED led1(16); 

void LED_ON() 
{
    led1.ON();
}

void LED_OFF() 
{
    led1.OFF();
}

#ifdef __cplusplus
}
#endif
```