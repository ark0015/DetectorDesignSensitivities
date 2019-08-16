# DetectorDesignSensitivities
Generates characteristic strain plots and signal-to-noise (SNR) waterfall plots for any gravitational wave (GW) detector sensitivity in the frequency domain and for various binary black hole (BBH) sources.

## `StrainandNoise.py`
Includes various classes that instantiate different GW detector sensitivities and Source strains: 
* Different GW detector sensitivities:
	* Pulsar Timing Arrays (PTAs)
	* Space Based Detectors (eg. LISA)
	* Ground Based Detectors (eg. LIGO,ET)
* Black Hole Binary (BHB) Source Strains:
	* Frequency evolving "chirping" sources
	* Monochromatic, non-evolving sources
* Time Domain Strains that can be converted to frequency space for use in SNR calculations
* Various strain conversions

## `SNRcalc.py`
Includes various functions to calculate the SNR for any given GW detector sensitivity using different methods for particular sources.

* __`getSNRMatrix(source,instrument,var_x,sampleRate_x,var_y,sampleRate_y)`__
	* Uses the variables given (`var_x`,`var_y`) and the their data range from `Get_Samples` in to sample the space either logrithmically or linearly based on the selection of variables. Then it uses `checkFreqEvol` to determine whether to use `calcMonoSNR` for a monochromatic source, or `calcChirpSNR` for evolving/chirping sources. It uses the aformentioned functions to compute the SNR for each value in the sample space. It returns the variable ranges used to calculate the SNR for each matrix and the SNRs with size of the sample_xXsample_y.
* __`calcMonoSNR`__
	* Uses a similar method to Robson,Cornish,and Liu 2018 (https://arxiv.org/abs/1803.01944) ~pg.9 to calculate the SNR for an non-evolving/monochromatic source.
* __`calcChirpSNR`__
	* Calculates evolving source using non-precessing binary black hole waveform model in `IMRPhenomD.py`, see Husa et al. 2016 (https://arxiv.org/abs/1508.07250) and Khan et al. 2016 (https://arxiv.org/abs/1508.07253). Uses an interpolated method to align waveform and instrument noise, then integrates over the overlapping region. See eqn 18 from Robson,Cornish,and Liu 2018 (https://arxiv.org/abs/1803.01944).
* __`calcDiffSNR`__:
    * Calculates the SNR loss from the difference in EOB waveforms and numerical relativity. The strain is from Sean McWilliams in a private communication. Uses an interpolated method to align waveform and instrument noise, then integrates over the overlapping region.
* __`plotSNR`__
	* Generates the waterfall plot for the variables given to calcSNR 
## `IMRPhenomD.py`
Calculates evolving source using non-precessing binary black hole waveform model using the methods of Husa et al. 2016 (https://arxiv.org/abs/1508.07250) and Khan et al. 2016 (https://arxiv.org/abs/1508.07253).
* __`FunPhenomD(Vars,fitcoeffs,N,f_low=1e-9,pct_of_peak=0.01)`__
	* Generates the frequency domain amplitude of the inspiral-merger-ringdown signal from BBHs with mass ratio (q <= 18) and spins (|a/m|~0.85 or when q=1 |a/m|<0.98). The waveform appoximation method utilizes PN-expansions for the inspiral, fits to numerical relativity simulations for the merger and ringdown sections, and a connection region to join the two.
	* Takes in `Vars` to get the source mass ratio and spins,
    fitting coefficients for QNM type (`fitcoeffs`) , sampling rate (`N`), and includes options for an initial starting frequency of the inspiral in Mf (`f_low`) and ending frequency as a percent of the amplitude height at merger.
    * Returns the frequency, the Phenom amplitude of the inspiral-merger-ringdown in natural units (Mf,strain in M units)

