import subprocess
import time

# Define the path to adb executable
adb_bin = r"C:\Users\Administrator\AppData\Local\vysor\app-5.0.7\resources\app.asar.unpacked\native\win32\adb.exe "

# Function to execute adb commands
def cmd(command):
    try:
        result = subprocess.run(command, capture_output=True, text=True, check=True)
        return result.stdout
    except subprocess.CalledProcessError as e:
        print(f"An error occurred while running the command: {e}")
        print(f"Command output: {e.output}")
        return None

# Function to query connected devices
def query_devices_id():
    devices = cmd(adb_bin + "devices")
    if devices is None:
        return []
    devices = [d.split("\t") for d in devices.strip().split('\n')[1:]]
    devices = [d[0] for d in devices if d[1] == "device"]
    return devices

# Get initial list of connected devices
did = query_devices_id()
print("一共有{}台设备".format(len(did)))

# Send reboot command to each device
for d in did:
    c = adb_bin + "-s {} reboot".format(d)
    print("发送重启命令: {}".format(c))
    cmd(c)

# Set of devices to track for reconnection
did_set = set(did)

# Wait for reconnection with a timeout of 10 minutes (600 seconds)
timeout = 600  # seconds
check_interval = 30  # seconds

start_time = time.time()
reconnected_devices = set()

while time.time() - start_time < timeout:
    # Query the current list of connected devices
    current_devices = set(query_devices_id())
    
    # Check which of the initially connected devices have reconnected
    reconnected_devices = did_set.intersection(current_devices)
    
    if reconnected_devices == did_set:
        print("全部设备已重连成功.")
        break
    
    print("当前已重连设备: {}".format(len(reconnected_devices)))
    print("等待剩余设备重连...")

    # Wait for the next interval before checking again
    time.sleep(check_interval)

if reconnected_devices != did_set:
    print("部分设备在规定时间内未能重连成功.")
    missing_devices = did_set - reconnected_devices
    print("未重连设备ID: {}".format(", ".join(missing_devices)))
else:
    print("所有设备在规定时间内成功重连.")
