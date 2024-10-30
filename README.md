# Example นี้จะแสดงวิธีการกระพริบ LED โดยใช้ GPIO driver หรือใช้ไลบรารี led_strip สำหรับ LED ที่สามารถกำหนดได้ (addressable LED) เช่น WS2812 โดยที่ไลบรารี led_strip จะถูกติดตั้งผ่าน component manager

เริ่มจากการเลือกใน Show Example
![image](https://github.com/user-attachments/assets/a0565f2f-42ad-4f21-99a3-61b77131a662)

กด Create project แล้วจะพบหน้าต่างนี้
![image](https://github.com/user-attachments/assets/ed5035ab-e78f-4858-9ba2-685b17b1bdc0)

ให้เราเลือก port ให้ตรงกับบอร์ด esp เสร็จแล้วให้ Build และ Flash ลงบอร์ด เมื่อ Flash เสร็จแล้วจะพบข้อความประมาณนี้ดังนี้
![image](https://github.com/user-attachments/assets/7c40c32f-16f0-4d17-9cc9-f5faff3a5817)


ต่อไปจะกำหนดขาBLINK_GPIO เป็น 23เพื่อสั่งให้ledติดดับ
```
//#include <stdio.h>
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "driver/gpio.h"
#include "esp_log.h"
#include "led_strip.h"
#include "sdkconfig.h"

static const char *TAG = "example";

/* กำหนดให้ BLINK_GPIO เป็น 23 */
#define BLINK_GPIO 23 

static uint8_t s_led_state = 0;

#ifdef CONFIG_BLINK_LED_STRIP

// ... (โค้ดที่เกี่ยวข้องกับ LED Strip)

// ฟังก์ชันที่กระพริบ LED Strip
static void blink_led(void) {
    if (s_led_state) {
        led_strip_set_pixel(led_strip, 0, 16, 16, 16);
        led_strip_refresh(led_strip);
    } else {
        led_strip_clear(led_strip);
    }
}

// ฟังก์ชันในการกำหนดค่า LED Strip
static void configure_led(void) {
    ESP_LOGI(TAG, "Example configured to blink addressable LED!");
    led_strip_config_t strip_config = {
        .strip_gpio_num = BLINK_GPIO,
        .max_leds = 1, 
    };
    // ... (โค้ดที่เกี่ยวข้องกับการตั้งค่า LED Strip)
}

#elif CONFIG_BLINK_LED_GPIO

// ฟังก์ชันที่กระพริบ GPIO LED
static void blink_led(void) {
    gpio_set_level(BLINK_GPIO, s_led_state);
}

/*
ฟังก์ชันในการกำหนดค่า GPIO LED
static void configure_led(void) {
    ESP_LOGI(TAG, "Example configured to blink GPIO LED!");
    gpio_reset_pin(BLINK_GPIO);
    gpio_set_direction(BLINK_GPIO, GPIO_MODE_OUTPUT);
}

#else
#error "unsupported LED type"
#endif

void app_main(void) {
    configure_led();

    while (1) {
        ESP_LOGI(TAG, "Turning the LED %s!", s_led_state == true ? "ON" : "OFF");
        blink_led();
        s_led_state = !s_led_state;
        vTaskDelay(CONFIG_BLINK_PERIOD / portTICK_PERIOD_MS);
    }
}
```
ผลลัพธ์ไฟจะติดและดับตามสถานะใน

![image](https://github.com/user-attachments/assets/7c40c32f-16f0-4d17-9cc9-f5faff3a5817)
![image](https://github.com/user-attachments/assets/e6c2f850-492f-408b-9eca-6c264f0643be)
![image](https://github.com/user-attachments/assets/3801f5ff-8584-4d15-aef0-4ea1de84e3d4)
