
# Folia in Faust 

This project contains a Faust version from the original program of Folia (for double bass and realtime electronics) - composed by Kaija Saariaho. Electronic program was originally made by Jean-Baptiste Barrière.  
The official (and well sustained) program is a Max MSP program, and can be found on [Kaiha Saariaho website](http://jb.barriere.free.fr/KS-pieces/Folia-electronics.html). 

This work has been made as part of [Metamorphoses](https://www.grame.fr/articles/metamorphoses-erasmus-project-2023-2025) project, in order to engage a practical reflection about tools used for realtime signal processing in the field of electroacoustic and mixed contemporary music. It was made by Johann Philippe with GRAME.  
This work has been documented with a paper for "Journées de l'Informatique Musicales" (JIM) 2025 : 
* [Program](https://jimlac25.inria.fr/program/)
* [Paper](https://hal.science/hal-05102319)
* [Talk](https://www.youtube.com/live/3HebU_49CPI?si=RfIESoYIkJpL8xpH&t=6136)

With this Faust version, it is possible to perform this piece with almost any musical software/environment: 
* Reaper (or any DAW)
* Csound
* PD/Max
* Web
* ...

# Program and signal processing 

The signal processing FAUST files are placed in `dsp` folder. 

The core of the piece is based on three DSP files: 
* [delay.dsp](https://github.com/grame-cncm/Folia-in-Faust/blob/main/dsp/delay.dsp)
* [harm.dsp](https://github.com/grame-cncm/Folia-in-Faust/blob/main/dsp/harm.dsp)
* [verb_inf_fdn.dsp](https://github.com/grame-cncm/Folia-in-Faust/blob/main/dsp/verb_inf_fdn.dsp) 
* [gmem_spat_auto.dsp](https://github.com/grame-cncm/Folia-in-Faust/blob/main/dsp/gmem_spat_auto.dsp) and [gmem_spat_auto_stereo.dsp](https://github.com/grame-cncm/Folia-in-Faust/blob/main/dsp/gmem_spat_auto_stereo.dsp)

These are the core signal processing components of the program. 
The file `patch.png` describes routing information.

## Design decisions

Both `delay.dsp` and `harm.dsp` are accurate reproductions of their counterpart in the Max MSP official program. 
Though, some other parts needed to be translated with difficult design decisions. Main difficulty is for `verb_inf_fdn.dsp`, which tries to stick to the concept of infinite reverberation used originally. It is a simple FDN reverberation, based on delay times calculated with [pyroomacoustics](https://github.com/LCAV/pyroomacoustics) (in script folder). The mechanism to make it infinite is reproduced as accurately as it could be from Max MSP program : the duration of reverberation (feedback) is inversely proportional to the average input level : the less double bass sound is loud, the longer reverberation is. 
For reverberation algorithm itself, some other (better ?) strategies could have been considered : 
- Making an IR of the original reverberation (SPAT reverb), convolve it, and using a feedback mechanism to change reverb duration
- Trying to reproduce SPAT's perceptive parameters (envelopment, reverberance...) with the FDN reverb.    

In order to reproduce the original patch with more fidelity, it has been decided to stick to its sound design philosophy: adding a simple reverb bus as well as simple circular spatialization tools: 
* Reaverb (or any convolution reverb program) and an IR for a large hall : this reverberation is not to be considered as an actual effect, but more like a solution to gather acoustic and electronic sound.  
* `gmem_spat_auto.dsp` and `gmem_spat_auto_stereo.dsp` enables automatic spatialization. While it is not the same spatialization as the original program (using IRCAM's spat), it uses the exact same algorithm for sources movement description. 

# Building and using 

## Build 

Building the DSP files depends on the execution/control environment you want to target. 
If you want to target Reaper for example, you can decide to build all the components as VST's, or as JSFX. 
Though, depending on your hardware, JSFX version of infinite reverb might be heavy to compute in realtime. 

If you target Csound, also there are at least 2 options :
- Build Csound opcodes from DSP files using `faust2csound` script (or using [Faust Web IDE](https://faustide.grame.fr))
- Use Csound `faust` opcodes to JIT compile DSP files with LLVM

## Re-create the execution environment

The second step to reproduce the program is to implement routing described in `patch/patch.png`. This raw patch description is made with [Patchbook](https://github.com/SpektroAudio/Patchbook). Implementation will be different depending on the environment you decide to use for performance. 
The environment must be allow you to  
* Execute DSP code
* Route signals from components to components
* Use MIDI or OSC inputs to control the main effects levels 

The `folia_reaper` session can give an example on how routing is actually made. Though, depending on the hardware and OS you use, you won't be able to use it straight : the VST might not be recognized from a system to another. So you might need to replace FX with your freshly built VST/JSFX plugins in the Reaper tracks. 

## Experimental Web

Some unfinished work in the `web` folder corresponds to an attempt to create a [light web version](web/index.html). 

