# UR3 Writer

Dự án ROS 2 Humble mô phỏng robot UR3 viết chữ Đ trong Gazebo bằng MoveIt 2.
Dự án gồm hai hướng:

- `ur3_d`: tạo quỹ đạo chữ Đ bằng công thức hình học.
- `ur3_image_writer`: đọc ảnh, tạo skeleton và chuyển thành quỹ đạo Cartesian.

## Build

Build lần đầu :

```bash
cd ~/ros2_ws
source /opt/ros/humble/setup.bash
colcon build --symlink-install --packages-select ur3_d ur3_image_writer
source install/setup.bash
```

## Khởi động Gazebo, MoveIt và RViz

Mở Terminal 1:

```bash
cd ~/ros2_ws
source /opt/ros/humble/setup.bash
source install/setup.bash
ros2 launch ur3_d ur3_d.launch.py
```

Đợi Gazebo, RViz và MoveIt khởi động xong. Có thể kiểm tra:

```bash
ros2 node list | grep move_group
```

Kết quả phải có `/move_group`. Sau đó chọn một trong hai cách chạy dưới
đây, không chạy đồng thời hai node viết chữ.

## Cách 1: vẽ chữ Đ bằng waypoint hình học

Mở Terminal 2:

```bash
cd ~/ros2_ws
source /opt/ros/humble/setup.bash
source install/setup.bash
ros2 run ur3_d write_letter_d_node
```

Tham số nằm trong `ur3_d/config/writer.yaml`. Trong RViz:

- Màu xanh nhạt: đường chữ Đ dự kiến.
- Màu xanh đậm: đường TCP thực tế ghi lại khi robot chạy.

Cũng có thể khởi động simulation và tự chạy node sau thời gian chờ:

```bash
ros2 launch ur3_d ur3_d.launch.py start_writer:=true
```

## Cách 2: vẽ chữ Đ từ ảnh

Mở Terminal 2:

```bash
cd ~/ros2_ws
source /opt/ros/humble/setup.bash
source install/setup.bash
ros2 run ur3_image_writer write_image_node
```

Ảnh mặc định:

```text
ur3_image_writer/resources/images/anhchuD.png
```

Tham số nằm trong `ur3_image_writer/config/image_writer.yaml`. Một lần chạy
thành công có các log chính:

```text
Moving directly to 0.040 m above named target 'test_configuration'.
Created 3 stroke(s) from '.../anhchuD.png'.
Image Cartesian path fraction: 100.00%
Completed stroke 1/3.
Completed stroke 2/3.
Completed stroke 3/3.
Completed 3/3 image strokes.
```

### Thay ảnh đầu vào

1. Đặt ảnh PNG/JPEG trong `ur3_image_writer/resources/images/`.
2. Sửa `image_path` trong `config/image_writer.yaml`.
3. Dùng `invert: true` cho chữ tối trên nền sáng; dùng `false` cho chữ sáng
   trên nền tối.

## Chạy test

```bash
cd ~/ros2_ws
source /opt/ros/humble/setup.bash
source install/setup.bash
colcon test --packages-select ur3_d ur3_image_writer
colcon test-result --verbose
```
