# orin-sage-node
**General things to be done**      
   - Copy kubeconfig to your home directory
   - 

**Issue 1: WES tools were not being downloaded**   
- Cause: get_core_device() function in scrape-system-metrics.py (waggle-edge-stack/kubernetes/debug) is hardcoded to look for a device with IP address 10.31.81.1 
- Fix: 
  ```
  def get_core_device(devices: List[Device]) -> Device:
      for device in devices:
          if device.ip == "10.31.81.1":
              return device
      # Fallback
      if devices:
          return devices[0]
      raise KeyError("could not find core device")
  ```
**Issue 2: Faced some issues with temp file ownership**

- Fix:
  ```
  sudo rm -f /tmp/terminating-pods-* 
  sudo chmod 755 /tmp
   ```

**Issue 3: wes-device-labeler-x service failing**
- Cause: Incorrect Kubernetes node name - needs to match the MAC address format expected by the device labeler
- Fix:
  ```
  sudo systemctl stop k3s
   sudo nano /etc/rancher/k3s/config.yaml
   # Add: node-name: <MAC_ADDR>
   sudo systemctl start k3s
  ```
   Delete the old node and clean up associated pv
  ```
   sudo kubectl delete node <old_node_name>
   sudo kubectl delete pvc --all  # if vol have wrong node affinity (this happened for me)
  ```
  



**Issue 4: wes-update-waggle-ssh-keys service failing**
- Cause: /home/waggle/.ssh doesn't exist
- Fix:
  Create Waggle user first if it doesn't exist (sudo useradd -m -s /bin/bash waggle), and then
  ```
  sudo mkdir -p /home/waggle/.ssh
  sudo chown waggle:waggle /home/waggle/.ssh
  sudo chmod 700 /home/waggle/.ssh
  ```



  
