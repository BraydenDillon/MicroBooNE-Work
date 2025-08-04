# MicroBooNE-Work
Container for scripts used for data analysis of MicroBooNE data for SULI internship


NOTE: to get files used for pandora histogram, I changed the file input to the Write_txt_file.ipynb notebook to the root file I wanted to run the selection on (beam Off, Dirt, etc) and deleted the Signal and Category keys as they could not be calculated without truth information


# Lantern Cut Methodology
### Cut 0: Vertex Verification
I use the FoundVertex variable to see if LANTERN reconstructs an interaction vertex

### Cut 1: Vertex Fiducial
I use the variables vtx{X,Y,Z} to ensure that the vertex was reconstructed within the detector fiducial volume defined by existing Pandora selection:
* 21.5 < X < 234.8
* -95 < Y < 95
* 21.5 < Z < 966.8

### Cut 2: Primary Particle Containment
I use the combination of the variables trackStartPos{X,Y,Z} and trackIsSecondary to verify that each primary particle track from the interaction starts within the containment volume, also defined by the existing Pandora selection below. I only apply these restrictions to particles with trackIsSecondary == 0, meaning that the track is not a secondary, i.e. it is a primary particle.
* 10 < X < 246.35
* -106.5 < Y < 106.5
* 10 < Z < 1026.8

### Cut 3: Cosmic Cut
Lantern does not have a PID specifically for cosmic rays, so I use the variable vtxFracHitsOnCosmic, which details the fraction of all hits associated with the reco neutrino vertex that match cosmic-tagged pixels. I cut events where this fraction is greater than 0.1.

### Cut 4: Muon ID
This is the first cut that is very different from the Pandora selection. I utilize LANTERN's more robust particle ID to bypass the more involved cuts and simply check if there is at least one track reconstructed with trackPID = 13, which signifies a muon.

### Cut 5: One Muon Only
It is established that a common occurrence is for a true pion to be reconstructed as a second muon candidate. So to make a preliminary cut against pions I cut events where more than one muon is reconstructed with trackPID

### Cut 6: Muon Containment
For resolution purposes, we must ensure that the outgoing muon lies in the cintainment volume completely. I use trackEndPos{X,Y,Z} and the containment volume defined in cut 2 along with trackPID to cut out events where the muon is not 'contained'. Of note, this is the most significant cut to efficiency in the selection scheme.

### Cut 7: Muon Phase Space
This cut uses trackPID to identify the muon once again, then uses the reconstructed track kinetic energy, trackRecoE, and the rest energy of a muon to calculate the energy of the muon and ensure it is within the phase-space limits of 0.1 - 1.2 GeV. This is also a cut for the sake of event resolution

### Cut 8: N Protons and Prelim Shower Cut
This cut uses trackPID once again to ensure there is at least one track reconstructed with a PDG of 2212, or a proton. It also uses the showerIsSecondary variable to cut events that reconstruct a shower as a secondary, as this fits our signal definition. 

### Cut 9: Pionless
This is the major cut that satisfies a large part of our signal definition. I needed to ensure that no pions were reconstructed in passing events, but I did not trust LANTERN's pion ID to a comfortable extent after analyzing it. I trusted its Proton ID much more, so I use the trackPID variable to ensure that no tracks were reconstructed which were tagged as anything but a proton.

### Cut 10: Proton Phase Space
Similar to muons, we need to ensure that the protons reconstructed are within phase-space momentum limits. I use the same scheme with trackRecoE and the mass of a proton to ensure that the energy is in the limits 0.25 - 1.00 GeV.

### Cut 11: Showerless
Finally, our signal definition means there cannot be any showers reconstructed, so I use the variable nShowers, confining its value to zero, to make sure there are no showers reconstructed. 
