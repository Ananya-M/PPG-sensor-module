# PPG-sensor-module
A simulink based algorithm to investigate HRV(heart rate variability) parameters

Photoplethysmography (PPG) is a non-invasive optical technique that measures blood volume changes in the microvascular bed of tissue. By capturing variations in light absorption, PPG is widely used for monitoring cardiovascular parameters and assessing physiological responses. A key biomarker representing time fluctuations of intervals between normal consecutive heartbeats, heart rate variability (HRV) may non-invasively index efferent activity of the vagus nerve—a neuro-immuno-modulator in the immune response inflammatory pathway—allowing prediction of ICU indication[1] and mortality for patients aged 70 years and older hospitalized with coronavirus disease 2019 (COVID-19).

​By capturing variations in light absorption, PPG is widely used for monitoring cardiovascular parameters and assessing physiological responses.A key biomarker representing time fluctuations of intervals between normal consecutive heartbeats, heart rate variability (HRV) may non-invasively index efferent activity of the vagus nerve—a neuro-immuno-modulator in the immune response inflammatory pathway—allowing prediction of ICU indication and mortality for patients aged 70 years and older hospitalized with coronavirus disease 2019 (COVID-19).

A prototype device intended to measure a photoplethysmography (PPG) signal and output SDNN and RMSSD was designed and evaluated utilizing MATLAB R2021b, Simulink, and an Atmel Mega 2560 microcontroller—wherefrom numerical SDNN and RMSSD outputs obtained from the system are displayed on a personal computer (PC) screen.

Working the system
Overview 
Operation of the device may be divided into sections of PPG acquisition, analog signal conditioning, analog-to-digital conversion (ADC), digital signal conditioning, beat detection, beat rejection, abnormal beat checking, clock-mediated time interval recording, and SDNN and RMSSD calculation.
PPG Acquisition: Input Sensor Placement and Signal Transduction
An LED illuminates a human fingertip to obtain a PPG signal—the input of the device. The operating principle of photoplethysmography pertains to the amount of light absorbed and reflected in the microvascular tissue bed, which varies with blood volume. The PPG circuit utilizes a green LED—noted to be less susceptible to motion artifacts and demonstrates increased hemoglobin absorption—and a photoresistor in the reflective mode . The distance between the LED and photoresistor is 5.0 mm. A raw PPG signal is obtained as the output of this section, possessing a magnitude of a few millivolts (mV) as well as noise. 

Analog Signal Conditioning
The raw PPG signal is processed to reduce noise, enhance signal components of interest, and facilitate beat detection. The components of this section are a passive high pass filter, a non-inverting amplifier, and an active low pass filter. A normal PPG signal lies within a frequency range of 0.5 Hz to 10.0 Hz . The high pass filter is intended to remove noise due to motion artifacts, a DC offset voltage float, and low frequency signals occurring below 0.5 Hz. Devoid of the DC offset and noise, the semi-processed PPG signal is of the order of a few mV and requires amplification. A non-inverting amplifier, having high input impedance and low output impedance, produces an output in phase with the input. The low pass filter is intended to remove noise due to high frequency signals occurring above 10.0 Hz. 

Analog-to-Digital Conversion
The amplified signal is provided to analog input pin A0 of the Atmel Mega 2560 microcontroller. As this is a 10-bit microcontroller, it can detect 2^10=1024 discrete analog levels from 0 to 1023. 

Digital Signal Conditioning
To ensure that the PPG signal contains meaningful pulses, an 85th order digital finite impulse response (FIR) filter was generated in the Simulink Filter Designer. The FIR filter further smooths analog-filtered input PPG signals, which are provided to the beat detection block. An initial prototype of the device utilized a moving average filter that accepted M samples of input at a time and determined the average of those samples to produce a single point. Increasing the quantity of input points considered by the filter increased output smoothness; however, the FIR filter exhibited improved smoothing of noisy Arduino outputs relative to the moving average filter.

Beat Detection, Beat Rejection, Abnormal Beat Checking
Beat acceptance for interval calculations was established on four principles:

Checking for the greatest peak amplitude among time intervals to the left and right of the current time interval is completed—i.e., searching for local maxima via delay blocks in the beat detector. When compared to three delays on either side, a maxima should be greater than 60% of the average value of five prior data points.
A moving threshold allows observation of general trends in the PPG signal, wherefrom a threshold value is continually updated. A beat is registered if the signal crosses this threshold value; accordingly, the beats are detected in real time. A static threshold value for a normal PPG signal was initially utilized, but replaced as it had to be manually adjusted according to the morphology of each input PPG signal.
Beat rejection occurs if the calculated interval between two beats is less than one-third of a second or greater than 2 seconds.Abnormal beat rejection occurs for intervals that differ from their neighboring intervals by more than 20%.

Time Interval Recording and HRV Parameter Calculations 
Following each beat identification, the time interval until the next beat is recorded; the clock is subsequently reset, and SDNN and RMSSD values are calculated with fixed-point arithmetic utilizing Eq. [1] and Eq. [2], respectively. Resultant numerical values are displayed in Simulink Display blocks on the PC screen, in units of ms.

Codebase Overview

The analog circuitry described in `Analog_circuit.fig` can be replicated using the provided values, calculated based on cutoff frequencies and gains mentioned earlier.  you can test the Simulink subsystems by skipping the Arduino interface,by utilizing the PPG signal provided (`PPG_for_simulation`), derived from the Capnobase database.

Getting Started with Simulink

To test the Simulink subsystems:

1. Simulation Setup:
   - Set up your Simulink model(PPG_sensor_module.m) for testing.
   - Configure the Simulink solver to `fixedstepdiscrete`.
   - Set the sampling frequency to 300Hz.

2. Input Configuration:
   - Set the input source to "From Workspace" in your Simulink model.

3. Hardware Configuration:
   - Skip the Arduino interface for now by setting the hardware configuration to "None."
   - Utilize the provided PPG signal (`PPG_for_simulation`) for simulation testing. This signal is derived from the Capnobase database.

Feel free to customize the content based on additional details or specific instructions in your codebase.

