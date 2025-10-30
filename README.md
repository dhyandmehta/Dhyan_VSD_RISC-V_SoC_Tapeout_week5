

## 🧭 **WEEK 5 — OpenROAD Flow Setup, Floorplanning, and Placement**

### **Objective**

The objective of this week’s task is to set up the **OpenROAD Flow Scripts** environment and execute the **Floorplan** and **Placement** stages of the physical design flow.
This marks the transition from transistor-level SPICE design (Week 4) to backend implementation, where logical designs are transformed into physical layouts.

### **Importance of This Task**

After understanding transistor-level timing behavior, this task introduces **OpenROAD**, an open-source RTL-to-GDSII flow widely used in VLSI research and development.
By performing floorplanning and placement, we gain insight into:

* How design constraints are applied before routing.
* How standard cells are arranged to minimize delay, area, and congestion.
* How physical design choices affect timing, power, and manufacturability.

This forms the foundation of the complete physical design flow in ASIC implementation.

---

## ⚙️ **Part 1 — Installing OpenROAD on Ubuntu 24**

### **About OpenROAD**

**OpenROAD (Open Rapid Object-Oriented Design)** is an open-source, fully autonomous, end-to-end RTL-to-GDSII digital physical design toolchain for ASIC design.
It integrates multiple modules for:

* Synthesis
* Floorplanning
* Placement
* Clock Tree Synthesis
* Routing
* Signoff Analysis

It uses **Yosys** for logic synthesis and **OpenSTA** for static timing analysis, enabling a complete open-source digital backend flow.

---

## 🧩 **Installation Procedure**

> **Note:** Installation on Ubuntu 24 may be challenging due to compiler and dependency compatibility. Follow the steps carefully.

---

### **Step 1️⃣ — Install Build Tools and GCC-9**

The default GCC versions (11+) are incompatible with OpenROAD dependencies. GCC-9 is used for a stable build.

```bash
sudo apt update
sudo apt install build-essential git cmake swig python3-dev -y

# Install GCC-9
sudo add-apt-repository ppa:ubuntu-toolchain-r/test -y
sudo apt update
sudo apt install g++-9 -y
```

---

### **Step 2️⃣ — Build and Install OR-Tools (Critical Dependency)**

OR-Tools must be installed in `/usr/local` to ensure proper linkage.

```bash
git clone https://github.com/google/or-tools.git
cd or-tools
mkdir build && cd build
cmake -DBUILD_DEPS=ON -DCMAKE_BUILD_TYPE=Release ..
make -j$(nproc)
sudo make install
```

---

### **Step 3️⃣ — Build and Install `spdlog`**

```bash
cd ~
git clone -b v1.11.0 https://github.com/gabime/spdlog.git
cd spdlog
cmake -DCMAKE_BUILD_TYPE=Release -DSPDLOG_FMT_EXTERNAL=OFF .
make -j$(nproc)
sudo make install
```

#### **Patch OpenROAD to Link `spdlog` Manually**

Modify:

* `src/CMakeLists.txt`: Comment out `find_package(spdlog REQUIRED)`
* Root `CMakeLists.txt`: Add

  ```bash
  add_subdirectory(third-party/spdlog)
  ```

  after the existing `add_subdirectory(third-party)` line.

---

### **Step 4️⃣ — Build and Install gtest**

```bash
cd /usr/src/gtest
sudo cmake .
sudo make
sudo cp lib/libgtest*.a /usr/lib/
```

✅ **All dependencies successfully installed.**

![Dependencies](https://github.com/user-attachments/assets/73e93921-487d-43f3-a91c-008fd088cbb8)

---

### **Step 5️⃣ — Clone and Build OpenROAD**

```bash
cd $HOME/vsdflow
git clone https://github.com/The-OpenROAD-Project/OpenROAD.git
cd OpenROAD
git submodule update --init --recursive

mkdir build && cd build

# Run CMake with GCC-9
cmake .. \
-DCMAKE_BUILD_TYPE=Release \
-DCMAKE_CXX_FLAGS="-Wno-error" \
-DCMAKE_PREFIX_PATH="/usr/local" \
-DCMAKE_CXX_COMPILER=/usr/bin/g++-9

# Build and Install
make -j$(nproc)
sudo make install
```

📸 **CMake Configuration Completed**

![CMake Complete](https://github.com/user-attachments/assets/76bfe8e3-0f3f-48f2-9478-2680b2ce69db)

📸 **Build and Installation**

![Build Done](https://github.com/user-attachments/assets/5a61693a-3c76-4f8e-b69a-c256a8834236)
![Installation Success](https://github.com/user-attachments/assets/873332ff-6d65-4735-90cf-33d3bf17f561)

✅ **OpenROAD Successfully Installed**

![Final Install](https://github.com/user-attachments/assets/01bb32e3-663a-43bf-8980-8f7bb9e41414)

---

### **Verifying Installation**

Run:

```bash
openroad
```

You should see the OpenROAD prompt as shown below:

![OpenROAD Terminal](https://github.com/user-attachments/assets/cd37c721-f27a-4569-8683-d9db759c146b)

Test libraries and PDK files are visible in the test directory:

![Test Library](https://github.com/user-attachments/assets/5d8f0d38-6bd8-4b0c-a034-ed15856d2944)

---

## 🧱 **Part 2 — Floorplanning and Placement**

After installation, execute only the **Floorplan** and **Placement** stages using the OpenROAD Flow Scripts.

### **Procedure**

1. Launch the flow:

   ```bash
   ./flow.tcl -design <design_name> -init_floorplan
   ./flow.tcl -design <design_name> -place
   ```
2. Confirm:

   * Core area and die dimensions generated.
   * Standard cells successfully placed.
3. Verify logs and intermediate design outputs.

### **Expected Outputs**

* Floorplan view (core/die boundary visible).
* Placement layout (standard cells visible).

---

## 📑 **Summary**

During Week 5, the OpenROAD Flow environment was successfully set up on **Ubuntu 24 (WSL)**.
The **Floorplan** and **Placement** stages were executed successfully.

**Challenges Faced:**

* GCC version incompatibility (resolved by forcing GCC-9).
* Manual patching of `spdlog` linkage for successful build.

**Outcome:**

* Fully functional OpenROAD setup.
* Generated visual outputs for floorplan and placement.

This was done with the reference of this repo.
https://github.com/gauravacad/WEEK-5-OPENROAD/blob/main/README.md

The below image is mine
<img width="1564" height="978" alt="Screenshot from 2025-10-30 16-41-15" src="https://github.com/user-attachments/assets/b58a23c3-0d96-4a92-a32e-019c47efab07" />
