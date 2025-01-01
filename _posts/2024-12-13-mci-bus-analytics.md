---
layout: post
title: "Sting-Sense: Bus Analytics with Inertial Sensors"
date: 2024-12-13 14:16:19 17:00:00 +0100
description: my MCI project from FA24
excerpt: Sting-Sense provides visibility into Georgia Tech bus behavior at different times of the day. The system utilized inertial sensors and GPS technology to collect and analyze data from four bus routes.
---
Ever missed a bus on campus and wondered why traffic seems to flow so unpredictably? Or driven over a bumpy road and thought, "Someone should fix this!" At Georgia Tech, the Sting-Sense project is trying to tackle these issues, paving the way for smarter transportation solutions.

**The challenge: traffic and maintenance on a busy campus**. Urban campus transportation systems face numerous challenges, including traffic congestion and road maintenance. The Sting-Sense project addresses these issues by implementing an IoT system on GT’s bus network. This system provides visibility into bus behavior across different times of the day, enabling data-driven decision making.

<div style="text-align: center;">
    <iframe src="https://kpath1999.github.io/gtbusmap" width="80%" height="600px" style="border:none;"></iframe>
    <em><br>Fig. 1. <b>Dashboard MVP:</b> Sting-Sense visualization. You can see all the routes mapped out, with the various colors denoting the road conditions.</em>
</div>

**Hardware setup: compact yet powerful**. The Sting-Sense system consists of a custom-built hardware package featuring a microcontroller, a GPS module, and an inertial measurement unit (IMU) sensor. These devices were installed on buses serving four major routes: gold, green, blue, and red. The gold route, in particular, is a vital link between Georgia Tech’s central campus, Tech Square, and the Midtown MARTA Station.<sup>1</sup>

<div style="text-align: center;">
    <img src="/assets/mci/hardware-setup.png" width="70%" />
    <em><br>Fig. 2. <b>Hardware Setup:</b> Compact and efficient, designed to fit seamlessly into the campus bus fleet.</em>
</div>

**Data collection: metrics that matter**. To analyze bus behavior and road quality, the Sting-Sense system collects data on three key metrics:

1. Timing: Precise timestamps record the exact moment each data point is logged, enabling temporal analysis of traffic patterns.
2. Location: GPS tracks the real-time movement of buses, providing insights into route efficiency and congestion hotspots.
3. Movement: IMU sensors capture acceleration and orientation, crucial for detecting road anomalies and assessing driver performance.

The hardware setup took some time to build out, so we used the Sensor Logger app on an iPhone for the initial mapping of bus routes. It offers a range of sensors, including an accelerometer, gyroscope, GPS, barometer, and more. You can easily export all the data to a CSV for further processing.

<div style="text-align: center;">
    <img src="/assets/mci/sensor-logger.png" width="20%" /> 
    <em><br>Fig. 3. <b>Early Data Collection:</b> The Sensor Logger app provided a user-friendly interface for capturing multi-sensor data during the project's pilot phase.</em>
</div>

**Traffic congestion analysis**. We use GPS data to understand traffic patterns. Speed data from the GPS is grouped into four congestion levels (quartiles). This method helps us discern traffic patterns across the day and in different parts of campus. The slowest speeds (1st quartile) indicate high congestion.

```python
def calculate_traffic_congestion(df):
    speed_stats = df['speed'].describe()

    def calculate_congestion_level(row):
        speed = row['speed']
        if speed <= speed_stats['25%']:
            return 5  # Heavy congestion
        elif speed <= speed_stats['50%']:
            return 4  # Moderate-heavy congestion
        elif speed <= speed_stats['mean']:
            return 3  # Moderate congestion
        elif speed <= speed_stats['75%']:
            return 2  # Light congestion
        elif speed == -1.0:
            return 0  # Unavailable data
        else:
            return 1  # No congestion

    # Apply the function to each speed value
    return df.apply(calculate_congestion_level, axis=1)
```

**Road quality assessment**. Vertical acceleration data is used to assess road quality. A measure is created called Standard Deviation of Vertical Acceleration (SDVA). SDVA is calculated by dividing the variation in vertical movement by the average speed. This helps detect bumps or issues in the road.

\$$\ \text{SDVA} = \frac{\sigma(a_z)}{\bar{v}} $$ where $\sigma(a_z)$ is the standard deviation of vertical acceleration and $\bar{v}$ is the average speed over a segment. To focus on bumps and ignore small vibrations, the Butterworth low-pass filter is used. Thresholds are based on real-world data to classify road conditions into four categories.

```python
def calculate_road_condition(df, window_size=100):
    
    # Apply low-pass filter to vertical acceleration
    sampling_rate = 1 / df['seconds_elapsed'].diff().mean()
    cutoff_frequency = 2  # Hz
    df['z_filtered'] = butter_lowpass_filter(df['z'], cutoff_frequency, sampling_rate, order=4)

    # Calculate SDVA (Standard Deviation of Vertical Acceleration)
    df['sdva'] = df['z_filtered'].rolling(window=window_size).std()

    # Normalize SDVA by speed (to account for speed effects)
    df['sdva_normalized'] = df['sdva'] / (df['speed'] + 1)  # Adding 1 to avoid division by zero

    # Define thresholds for road condition classification
    conditions = [
        (df['sdva_normalized'] < 0.05),
        (df['sdva_normalized'] < 0.1),
        (df['sdva_normalized'] < 0.15),
        (df['sdva_normalized'] < 0.2),
        (df['sdva_normalized'] >= 0.2)
    ]

    values = [5, 4, 3, 2, 1]

    # Create road_condition column
    df['road_condition'] = np.select(conditions, values)

    return df
```

### Future Improvements

To maximize the impact of Sting-Sense, we plan to upgrade the system with cellular network connectivity. This will allow for real-time data uploads to the cloud, making information instantly accessible for analysis and decision-making.

Our big goal is to build a system that can work on its own. This means processing data as soon as it's collected and making decisions without human help. The implementation of predictive maintenance strategies in IoT systems in transportation brings several benefits.

- It allows for a shift from reactive to proactive maintenance, minimizing unplanned downtime and disruptions in transportation services.
- By addressing maintenance needs before failures occur, organizations can improve the reliability and availability of vehicles and infrastructure, enhancing passenger safety and customer satisfaction.

**Conclusion**. This pilot project can be improved further by adding more bus routes and including other campus vehicles. We could make this system even more useful by using machine learning to predict when buses need maintenance and finding better routes for buses. If we link Sting-Sense with other campus information like class schedules and event calendars, we could make the bus service respond better to campus needs.<sup>2</sup>

**What's next?** As we continue to improve Sting-Sense, Georgia Tech could set new standards for smart campus transportation and become a model for other schools and cities to follow. The project aligns with Georgia Tech's goal to have 100% clean transportation by 2030. By leveraging IoT technologies, we can optimize route planning, enhance passenger experiences, monitor vehicle health in real-time, improve safety, and enable demand-responsive transit services.<sup>3</sup>