# 🚦Traffic Sign Recognition on ESP32-CAM

This is an upgraded version of the traffic sign recognition project, now integrated with **ESP32-CAM microcontroller**, **FreeRTOS**, **TensorFlow Lite**, and a **mobile Android application** to display recognition results.

---

## 📌 Project Objectives

- Deploy a deep learning CNN model on an embedded ESP32-CAM device.
- Send recognition results to an Android app over local Wi-Fi.
- Use FreeRTOS to manage tasks: image processing, classification, and communication.

---

## 🧰 Components & Technologies

- ESP32-CAM
- Languages: Python (training), C++ (firmware), Java (Android)
- Technologies: CNN, TensorFlow Lite, FreeRTOS, TCP Socket

---

## ⚙️ System Workflow

### 🧩 FreeRTOS Task Structure

- **Task 1**: Initialize camera, preprocess image, run CNN model and save output
- **Task 2**: Extract class with highest probability and send result via server variable
- **Task 3**: Run socket server to deliver classification output to Android app

### 📱 Android App

- Connects to ESP32-CAM via local Wi-Fi
- Listens for classification data
- Displays result and audio alerts

---

## 🧠 CNN Model

```python
# 9 main layers:
model = Sequential([
    Conv2D(32, (3,3), activation='relu', input_shape=(32,32,1)),
    Conv2D(64, (3,3), activation='relu'),
    MaxPooling2D(2,2),
    Dropout(0.25),
    Flatten(),
    Dense(128, activation='relu'),
    Dropout(0.5),
    Dense(3, activation='softmax')
])
```

- Convert the model to `.tflite` format
- Use `xxd` to convert `.tflite` → C array (embedded into ESP32 firmware)

---

## 📷 Image Preprocessing on ESP32-CAM

```c
// Convert to grayscale, resize to 32x32, and normalize
for (int y = 0; y < height; y++) {
    for (int x = 0; x < width; x++) {
        gray = (0.3 * R + 0.59 * G + 0.11 * B);
        normalized = gray / 255.0f;
    }
}
```

---

## 📡 Communication Between ESP32-CAM & Android

```cpp
// ESP32 TCP Server
WiFi.softAP("TrafficSignESP", "12345678");
WiFiServer server(8888);
WiFiClient client = server.available();
client.write(outputLabel.c_str());
```

```java
// Android Client
Socket socket = new Socket("192.168.4.1", 8888);
InputStream input = socket.getInputStream();
// Receive and display the traffic sign label
```

---

## 📈 Results & Limitations

### ✅ Achievements

- Accurately detects 3 traffic signs with >90% accuracy
- Successfully sends recognition results to Android
- Runs stably on ESP32-CAM with 3 concurrent FreeRTOS tasks

### ⚠️ Limitations

- ESP32-CAM lacks processing power for complex deep learning models
- Limited RAM, CPU speed, and image resolution
- Limited support for deep learning libraries on ESP32

---

## 🌱 Future Improvements

- Expand dataset with more traffic signs under various conditions
- Optimize model with MobileNetV2 or quantized CNNs
- Improve Android UI/UX
- Test deployment in real-world environments (e.g., smart cars, IoT systems)

---

## 👨‍🔬 Team Members
- Phan Xuân Huynh - N20DCCN023
- Đinh Hồng Kông – N20DCCN029
- Phan Văn Lục - N20DCCN037
- Nguyễn Trần Trọng Tín – N20DCCN065
- Đặng Khắc Toản – N20DCCN067
---

## 📚 References

- [FreeRTOS on Arduino](http://arduino.vn/bai-viet/1673-chay-da-nhiem-tren-arduino-voi-freertos)
- [CNN Algorithm - TopDev](https://topdev.vn/blog/thuat-toan-cnn-convolutional-neural-network/)
