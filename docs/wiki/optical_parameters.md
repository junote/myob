
- tx/rx-power
- frequency
- SNR **Signal-to-Noise Ratio** 
	- GSNR **Gerneralized SNR**
		- SNRase: SNR from OSNR
		- SNRnli: SNR computed using gaussian noise model
		- SNRgawbs: Caused by guided acoustic wave Brillouin scattering布尔分布

	- ESNR, **Estimated Signal-to-Noise Ratio**，
		- estimation of the SNR for a given signal.
		- calculated by dividing the **power of the desired signal** by the **estimated noise power**.
		 - estimated noise power can be obtained through various methods, such as statistical analysis, adaptive filtering, or signal subspace projection.
	- **Optical Signal-to-Noise Ratio (OSNR)**
		- OSNR quantifies optical noise interference on optical signals.
- **CD**, chromatic dispersion
	- the change in refractive index due to a change in wavelength. Dispersion is the phenomenon that causes light to be separated into various colors while passing through a prism.
- **PDL**, polarization dependent losses
	- ![[Pasted image 20250305100404.png]]
- **DGD**, differential group delay
	- ![[Pasted image 20250305100441.png]]
- **So-DGD**,
- pre/post FEC error ration,
- **Q-Factor (Quality Factor)**
	- Q-factor characterizes the strength of damping in an oscillator or resonator.
	- Higher Q-factor implies less damping and narrower line-width.

- laser bias current,
- ZR frame related (
	- BBE, **Background Block Error**
		- the number of erroneous data blocks per million data blocks transmitted
	- ES, **Errored Seconds**
		- the number of seconds during which the received data contains one or more bit errors.
	- SES, **Severely Errored Seconds
		- the number of seconds during which the received data contains a high number of bit errors
	- UAS Unavailable Seconds
		- the number of seconds during which the network is unavailable.
	- uncorrected word,
	- corrected bytes
	- [BBE, ES, SES and UAS relation in OTN – MapYourTech](https://mapyourtech.com/bbe-es-ses-and-uas-relation-in-otn/)