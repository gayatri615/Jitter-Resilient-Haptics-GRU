# Predictive Kinematic Buffering for Jitter-Resilient Haptic Teleoperation over 5G/Cloud Architectures

## Project Overview
In remote robotic applications such as telesurgery or hazardous waste management, human operators rely on high-frequency haptic feedback loops (~1kHz) to control physical robotic manipulators. However, real-world 5G and Cloud architectures suffer from chaotic network jitter (sudden, unpredictable drops or spikes in data latency). 

When a telemetry packet is delayed or dropped, standard systems apply a Zero-Order Hold (freezing the last known coordinate). This causes a dangerous phenomenon known as **limit cycle oscillations**—where the physical robot overcorrects its position violently upon recovering connectivity, risking hardware destruction or patient harm.

This project implements a **Software-in-the-Loop (SIL) Digital Twin** that evaluates a lightweight, deployable **Causal Gated Recurrent Unit (GRU)** at the edge. By shifting the predictive paradigm from absolute spatial tracking to relative velocity-delta ($\Delta x$) prediction, the system intelligently buffers and reconstructs human intent in real time during network dropouts without introducing computational lag.

---

## The Research Gap Addressed
1. **Active Prediction vs. Passive Dampening:** Traditional industrial methods mitigate jitter using fixed passivity dampening (adding permanent artificial mechanical drag). While safe, this makes the robot feel sluggish and non-responsive. This project introduces **Dynamic Intermittent Autonomy**, actively synthesizing the path only when network drops occur.
2. **The Causal Real-Time Constraint:** Many research papers use *Bidirectional* deep learning networks (LSTMs/GRUs) for trajectory smoothing. However, Bidirectional layers process data from past-to-future and future-to-past, meaning they require future packets to exist—rendering them physically impossible to deploy in a real-time streaming environment. This project resolves this by conducting a rigorous **Ablation Study**, benchmarking a deployable **Causal GRU** against a non-causal "Offline Oracle" baseline.

---

##  Tech Stack & Architecture
* **Language:** Python 3.x
* **Deep Learning Framework:** TensorFlow / Keras (GRU Core)
* **Data Processing & Analytics:** NumPy, Pandas, Scikit-Learn
* **Physics Simulation:** Mathematical Mass-Spring-Damper differential equation solver
* **Visualization Suite:** Matplotlib, Seaborn

---

## Performance & Benchmark Metrics

The system was evaluated using a heavy-tailed **Pareto Distribution** to simulate severe, bursty 5G network degradation over a 10,000ms telemetry stream:

| Evaluation Metric | Baseline (No Buffer) | Model B (Non-Causal Oracle) | Model A (Our Causal Edge GRU) |
| :--- | :--- | :--- | :--- |
| **Trajectory Path RMSE** | 0.003193 | 0.001138 | **0.000674** |
| **True Accuracy Boost (%)** | 0.00% (Reference) | 64.36% Improvement | **78.88% Improvement** |
| **Real-Time Deployable?** | Yes | No (Requires Future Packets) | **Yes (Purely Causal)** |

### Key Takeaways:
* **Model A (Causal Edge GRU)** achieved a massive **78.88% reduction in path error**, outperforming the non-causal oracle. Because human motor movement is bound by physical momentum, focusing 100% of the GRU's update and reset gates on historical velocity trends yielded a much cleaner kinematic extrapolation.
* The model converged to an exceptionally stable validation loss of **$4.67 \times 10^{-7}$**, making it highly reliable for microsecond-level edge loops.

---

## Visual Analysis & Validation

### 1. Model Convergence Profile
![Model Convergence](./assets/convergence_history.png)
* **Explanation:** This plot captures the training and validation MSE profiles over 10 epochs. Both Model A and Model B demonstrate stable, rapid asymptotic decay toward zero without any geometric signs of overfitting or gradient explosions, verifying the mathematical reliability of our training pipeline.

### 2. Mechanical Limit Cycle Suppression
![Mechanical Joint Simulation](https://github.com/gayatri615/Jitter-Resilient-Haptics-GRU/blob/main/Mechanical%20Plant%20Joint%20Response%20Simulation%20.png?raw=true)
* **Explanation:** This chart displays the physical reaction of a simulated robotic joint (Mass-Spring-Damper plant). The red dashed line shows how a raw, unbuffered network drop causes violent position overcorrections and oscillations. The blue line shows our **Causal Edge GRU** dynamically filling the data gaps, smoothing out the physical control actions to match the ideal path trajectory perfectly.
