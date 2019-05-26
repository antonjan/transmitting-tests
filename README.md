

Transmitter test

![modulator and transmitter](/transmitter-1.png?raw=true)

Some time ago I saw an interesting program on the net, the *rpitx* programs
Since I was starting to experiment with the hackrf, I took some of the
sources and built a simple "iq-server", a program that takes iq samples
as input and generates a VHF signal on the rpi. Since the performance
was - as expected - poor, I also built a server for generating
VHF signals using the hackrf.

In order to ease experimenting, the software is structured in
components, as servers
and modulators in separate programs. Communication between the 
components is with I/Q samples - encoded as two 16 bit integers -
over an IP port.
Defaults are "127.0.0.1" as url, i.e. "localhost", and port 8765
for the servers, and port 8766 and 8767 for input to the fm-streamer.

-------------------------------------------------------------------------
*Servers
------------------------------------------------------------------------

Two "servers" are 

* the iq-server for the rpi
* the iq-server for the hackrf

Both servers are designed to operate as separate programs, taking
I/Q samples as input, using an ip port (default 8765).
Parameters are the port, the rate for the incoming IQ samples
and the
"transmission" frequency.
Defaults are set, so that - when running
on one computer, programs can be connected without further specification.

I intend to create a simple third server with the same input parameters
that sends the I/Q samples to a soundcard. 

The - longer term - objectives are
(i)  to build some components with which
input from microphone and PCM encoded recordings can be mixed and
send to either an AM modulator, an FM modulator, directly to a soundcard
or to /dev/null
(ii) to couple the fm-streamer to the DAB programs, such that the
audio output of the DAB decoder can be received on my antique and
portable radios.

-------------------------------------------------------------------------
* iq-server for the rpi
-------------------------------------------------------------------------

The iq-server for the rpi is derived from the rpitx software.
It takes as input I/Q samples with a rate specified in the
command line, from an ip port, specified in the command line
(or taken by default), on a frequency, specified in the command line.

From a practical point of view, the use of the rpi-server is limited,
it is not able to handle higher incoming samplerates.

-------------------------------------------------------------------------
* iq-servers for the hackrf
-------------------------------------------------------------------------

The hackrf-transmitter for the hackrf shows a widget. The samplerate and
ip port for the incoming IQ samples are to be specified in the
command line. The frequency can be selected on the widget, the widget
will show the spectrum of the incoming I/Q signals.

Note that in the current state, the cpu of the rpi will overload
when running the hackrftransmitter and modulator. Some profiling
is required.

The hackrf-server is the version without a gui. Parameters are
the frequency to be used, the sample rate for the incoming data
and the gain. Of course the port to which we are listening can also
be specified.

-------------------------------------------------------------------------
*Modulators
-------------------------------------------------------------------------

A number of modulators is built. The modulators are

* an iq modulator
* two am modulators, one for file input and one for speech
* two fm modulators, one with a (kind of) GUI and one command line driven
* a sweep generator
* an fm-streamer

-----------------------------------------------------------------------
* iq modulator
------------------------------------------------------------------------

The iq modulator takes as input a file as generated by my sw software.
I use the iq modulator, together with the hackrf module to replay
recordings with data containing a drm transmission
The outputof the modulator is sent to a specified (url, port).

-------------------------------------------------------------------------
* am modulator
-------------------------------------------------------------------------

The am modulator takes a file as input, the sample rate will be converted
to the rate specified in the command line (or the default) and
transformed into I/Q samples that are sent to a specified (url, port) ip
address.

-------------------------------------------------------------------------
* speech modulator
-------------------------------------------------------------------------

the speech modulator takes - as the name suggests - input from the
(currently) default input soundcard device, the microphone on my laptop.
The input samplerate, 44100 by default, will be converted to the
rate specified in the command line (or the default one) and send as I/Q
samples to a specified (url, port) ip address.

A command line option is to select the output to be an AM, and USB
or an LSB signal.

------------------------------------------------------------------------
* fm-streamer
--------------------------------------------------------------------------

The fm-streamer - not to be confused with the fm-qt-modulator -
takes as input PCM data (2 channels).
The input will be converted into a full stereo signal.
The signal allows for RDS messages, however, in the current form
the streamer does not have a defined input channel for the RDS.
The fm streamer has two input channels, one for PCM data, the other
one for RDS messages

------------------------------------------------------------------------
* the PCM handler
------------------------------------------------------------------------

The PCM handler takes as input a single PCM encoded file (a "wav" file)
or a "play list", i.e. a file containing the names of the "wav" files
to be procesed.

An example of the list 

	./Un_Poco_Loco.wav
	./Vergeet_me_niet.wav
	./Spiegel im Spiegel.wav

The output of the PCM handler is 2 channel PCM data, ready for
being processed by the fm-streamer and - as rds data - the name
of the file the content of which is being played.

------------------------------------------------------------------------
* fm-qt-modulator
------------------------------------------------------------------------

the fm-qt-modulator - not to be confused with the fm-modulator -
shows a widget on which a file to input can be selected. The widget
will show on a display the spectrum of the generated I/Q signal.
Not that - at least currently - the signal is mono and not equipped
with RDS

-------------------------------------------------------------------------
* sweep modulator
-------------------------------------------------------------------------

the sweep modulator just generates a sweeping I/Q signal.


