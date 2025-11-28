import cv2
import requests
from ultralytics import YOLO

# ==== CONFIG =====
STREAM_URL = "http://192.168.100.106:81/stream"   # ESP32-CAM stream URL
ESP32_URL  = "http://192.168.100.107/update"         # CHANGE to your ESP32 IP!


CAR_IDS = [2, 5, 7] 

def main():
    print("Loading YOLOv8n model (first time may take some time)...")
    model = YOLO("yolov8n.pt")  

    cap = cv2.VideoCapture(STREAM_URL)
    if not cap.isOpened():
        print("ERROR: Cannot open camera stream")
        return

    print("Stream opened, starting detection. Press 'q' to quit.")

    while True:
        ret, frame = cap.read()
        if not ret or frame is None:
            print("Failed to grab frame")
            continue

        
        results = model(
            frame,
            imgsz=640,
            conf=0.3,
            verbose=False
        )

        r = results[0]
        boxes = r.boxes

        # Count vehicles
        car_count = 0
        if boxes is not None and boxes.cls is not None:
            cls_list = boxes.cls.tolist()
            for c in cls_list:
                if int(c) in CAR_IDS:
                    car_count += 1

       
        annotated = r.plot()  

        
        cv2.putText(
            annotated,
            f"Cars: {car_count}",
            (10, 30),
            cv2.FONT_HERSHEY_SIMPLEX,
            1,
            (0, 255, 0),
            2
        )

        cv2.imshow("Car Detection (YOLOv8)", annotated)

        
        try:
            requests.get(f"{ESP32_URL}?cars={car_count}", timeout=0.2)
        except Exception as e:
           
            print("ESP32 send error:", e)

        
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

    cap.release()
    cv2.destroyAllWindows()


if __name__ == "__main__":
    main()
