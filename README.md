# Autonomous Crash & Ad Mediation Diagnostic Engine

## Executive Summary
Mobile gaming revenue heavily relies on third-party ad mediation SDKs (e.g., AppLovin, IronSource, UnityAds). However, toxic interactions between specific SDK versions, OS environments, and hardware constraints frequently cause fatal memory leaks and "Silent Fails" (invisible revenue drops where ad-fill returns 0 without triggering a crash flag). Standard deterministic monitoring completely misses these edge cases. 

This project engineers an autonomous DevOps diagnostic pipeline. Using Unsupervised Anomaly Detection, Association Rule Mining, and Biostatistical Survival Analysis, the engine automatically isolates invisible revenue leaks, mathematically proves the root causes of hard crashes, and probabilistically models player session degradation under cumulative memory stress.

## Pipeline Architecture

### 1. High-Volume Infrastructure Telemetry Simulation
Generated a 1-million-row production dataset simulating server ad requests across diverse hardware profiles (RAM tiers, OS versions). To rigorously evaluate the machine learning layers, specific architectural "diseases" were mathematically injected into the data:
* **Silent Fails:** AppLovin SDK v4.1 generating zero-fill-rate timeouts without fatal exception flags.
* **OOM (Out of Memory) Leaks:** Toxic combinations of 2GB RAM devices loading heavy UnityAds Rewarded Video payloads.
* **ANR (Application Not Responding):** Thread-blocking latency spikes tied to deprecated Android 10 interactions.

### 2. Unsupervised Anomaly Detection (Isolation Forest)
Standard SQL-based crash dashboards rely on explicit error flags, allowing 'Silent Fails' to bleed revenue invisibly. I deployed an **Isolation Forest** (an unsupervised, tree-based ML algorithm) to analyze the multivariate geometry of `load_time_ms` and `fill_rate` across non-crashing logs. 
* **Result:** The model successfully identified 47,810 silent anomalies, autonomously isolating the AppLovin v4.1 SDK as the primary driver of invisible revenue bleed without requiring any pre-labeled data.

### 3. Root Cause Diagnostics (Association Rule Mining)
To eliminate manual, trial-and-error debugging by DevOps teams for hard crashes (`OOM_Exception` and `ANR_Timeout`), the **Apriori Algorithm** was deployed to mine frequent pattern itemsets across the telemetry matrix. 
* **Result:** By enforcing strict Confidence and Lift thresholds, the algorithm programmatically isolated the exact toxic infrastructure combinations. It generated a mathematical proof (Confidence = 1.0) that the `UnityAds SDK + 2GB RAM` combination is strictly responsible for Out-Of-Memory app explosions.

### 4. Probabilistic Session Degradation (Survival Analysis)
To solve the business problem of static ad-caps, I applied **Kaplan-Meier Survival Analysis** (a probabilistic estimator used in medical research) to model the lifecycle of a player *per session*. 
* **The Engineering Context:** Poor garbage collection in ad SDKs causes cumulative heap allocation (a memory leak). This means crashes happen based on consecutive ad loads in a *single sitting*, not daily volume.
* **Result:** The survival curves mathematically quantify real-time hardware degradation. The model proves that a 2GB device crosses a critical 50% crash probability threshold after exactly 7 consecutive ad impressions. This allows the backend to implement **Dynamic Ad-Throttling**—forcing the app to clear its cache or stop serving ads to low-RAM users before a fatal memory exception occurs, preserving the user session and App Store rating.

## Tech Stack
* **Data Engineering:** Python, Pandas, NumPy
* **Unsupervised Machine Learning:** scikit-learn (Isolation Forest)
* **Association Rule Mining:** mlxtend (Apriori)
* **Probabilistic Modeling:** lifelines (Kaplan-Meier Estimator)
