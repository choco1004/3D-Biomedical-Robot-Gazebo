

# 3D-Biomedical-Robot-Gazebo
3D Biomedical Robot Gazebo
<wsl>
import rclpy
from rclpy.node import Node
from sensor_msgs.msg import LaserScan
from geometry_msgs.msg import Twist

class ObstacleAvoidance(Node):
    def __init__(self):
        super().__init__('obstacle_avoidance')
        self.publisher_ = self.create_publisher(Twist, 'cmd_vel', 10)
        self.subscription = self.create_subscription(
            LaserScan,
            'scan',
            self.scan_callback,
            10)

    def scan_callback(self, msg):
        min_distance = min(msg.ranges)
        twist = Twist()
        if min_distance < 0.5:
            twist.angular.z = 0.5  # Turn if obstacle is close
        else:
            twist.linear.x = 0.2  # Move forward
        self.publisher_.publish(twist)

def main(args=None):
    rclpy.init(args=args)
    node = ObstacleAvoidance()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
import rclpy
from rclpy.node import Node
from sensor_msgs.msg import LaserScan
from geometry_msgs.msg import Twist

class ObstacleAvoidance(Node):
    def __init__(self):
        super().__init__('obstacle_avoidance')
        self.publisher_ = self.create_publisher(Twist, 'cmd_vel', 10)
        self.subscription = self.create_subscription(
            LaserScan,
            'scan',
            self.scan_callback,
            10)

    def scan_callback(self, msg):
        min_distance = min(msg.ranges)
        twist = Twist()
        if min_distance < 0.5:
            twist.angular.z = 0.5  # Turn if obstacle is close
        else:
            twist.linear.x = 0.2  # Move forward
        self.publisher_.publish(twist)

def main(args=None):
    rclpy.init(args=args)
    node = ObstacleAvoidance()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()

    2. setup.py
<wsl>
nano ~/ws/src/drug_delivery_hospital/setup.py

</>python
from setuptools import setup

package_name = 'drug_delivery_hospital'

setup(
    name=package_name,
    version='0.0.1',
    packages=[package_name],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='seohee',
    description='Obstacle avoidance robot',
    entry_points={
        'console_scripts': [
            'obstacle_avoidance = obstacle_avoidance:main',
        ],
    },
)

3. LiDAR 센서 추가 (model.sdf 수정)

<link name="lidar_link">
  <pose>0 0 0.3 0 0 0</pose>

  <sensor name="lidar" type="gpu_lidar">
    <update_rate>10</update_rate>
    <ray>
      <scan>
        <horizontal>
          <samples>360</samples>
          <min_angle>-3.14</min_angle>
          <max_angle>3.14</max_angle>
        </horizontal>
      </scan>
      <range>
        <min>0.1</min>
        <max>10.0</max>
      </range>
    </ray>

    <plugin name="gz_ros_lidar" filename="libgazebo_ros_ray_sensor.so">
      <topicName>/scan</topicName>
    </plugin>
  </sensor>
</link>

4. commit 

cd ~/ws
colcon build
source install/setup.bash

ros2 launch drug_delivery_hospital gz_sim.launch.py

ros2 run drug_delivery_hospital obstacle_avoidance
