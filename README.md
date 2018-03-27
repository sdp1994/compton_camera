# setup, build, and run simulation
Requires Geant4 and ROOT. I use Geant4 10.02.p02 but the specific version shouldn't matter too much.

If you want visualization, Geant4 must be installed with QT, example:

```cmake ~/src/geant4.10.02.p02/ -DCMAKE_INSTALL_PREFIX="/home/meeg/software/geant4.10.02.p02" -DGEANT4_USE_QT=ON```

to build:
* ```git clone https://github.com/meeg/compton_camera.git```
* ```cd compton_camera```
* ```source ~/software/geant4.10.02.p02/bin/geant4.sh```
* ```mkdir build```
* ```cmake ..```
* ```make```

Run in the `build` directory.
* For a console with visualization just run `./exampleB2a` (this runs the macro `init_vis.mac`).
* To run a batch of 1e8 events run `./exampleB2a -m run2.mac`.

The particle source is defined in `init_source.mac`.

Whichever way you run the simulation, it will make the output file `B4.root`.

# looking at the data

to make a plot of hit positions in the 0th ALPIDE:

B4->Draw("posYVec:posXVec>>(50,-15,15,50,-7.5,7.5)","chamberNbVec==0","colz")

# original README from Geant4 example B2a

$Id: README 95508 2016-02-12 13:52:06Z gcosmo $
-------------------------------------------------------------------

     =========================================================
     Geant4 - an Object-Oriented Toolkit for Simulation in HEP
     =========================================================

                            Example B2
                            ----------

 This example simulates a simplified fixed target experiment.

 1- GEOMETRY DEFINITION
 
     The setup consists of a target followed by six chambers of increasing
     transverse size at defined instances from the target. These chambers are 
     located in a region called the Tracker region. 
     Their shape are cylinders, constructed as simple cylinders
     (in B2aDetectorConstruction) and as parametrised volumes 
     (in B2bDetectorConstruction), see also B2bChamberParameterisation class.

     In addition, a global, uniform, and transverse magnetic field can be 
     applied using G4GlobalMagFieldMessenger, instantiated in 
     B2[a,b]DetectorConstruction::ConstructSDandField with a non zero field value,
     or via interactive commands. 
     For example:

     /globalField/setValue 0.2 0 0 tesla
 
     An instance of the B2TrackerSD class is created and associated with each 
     logical chamber volume (in B2a) and with the one G4LogicalVolume associated 
     with G4PVParameterised (in B2b). 

     One can change the materials of the target and the chambers
     interactively via the commands defined in B2aDetectorMessenger
     (or B2bDetectorMessenger). For example:

     /B2/det/setTargetMaterial G4_WATER
     /B2/det/setChamberMaterial G4_Ar
 
 2- PHYSICS LIST

     The particle's type and the physic processes which will be available
     in this example are set in the FTFP_BERT physics list. This physics list 
     requires data files for electromagnetic and hadronic processes.
     See more on installation of the datasets in Geant4 Installation Guide,
     Chapter 3.3: Note On Geant4 Datasets:
     http://geant4.web.cern.ch/geant4/UserDocumentation/UsersGuides/InstallationGuide/html/ch03s03.html
     The following datasets: G4LEDATA, G4LEVELGAMMADATA, G4SAIDXSDATA and
     G4ENSDFSTATEDATA are mandatory for this example.
   
     In addition, the build-in interactive command:
	         /process/(in)activate processName
     allows the user to activate/inactivate the processes one by one.

 3- ACTION INITALIZATION

   A newly introduced class, B2ActionInitialization,
   instantiates and registers to Geant4 kernel all user action classes.
   
   While in sequential mode the action classes are instatiated just once,
   via invoking the method:
      B2ActionInitialization::Build() 
   in multi-threading mode the same method is invoked for each thread worker
   and so all user action classes are defined thread-local.

   A run action class is instantiated both thread-local 
   and global that's why its instance has is created also in the method
      B2ActionInitialization::BuildForMaster() 
   which is invoked only in multi-threading mode.

 4- PRIMARY GENERATOR

     The primary generator action class employs the G4ParticleGun. 
     The primary kinematics consists of a single particle which hits the target 
     perpendicular to the entrance face. The type of the particle 
     and its energy can be changed via the G4 built-in commands of 
     the G4ParticleGun class. 
  
 5- RUNS and EVENTS
 
     A run is a set of events.
 
     The user has control:
        - at Begin and End of each run (class B2RunAction)
        - at Begin and End of each event (class B2EventAction)
        - at Begin and End of each track (class TrackingAction, not used here)
	- at End of each step (class SteppingAction, not used here)
        
     The event number is written to the log file every requested number 
     of events in B2EventAction::BeginOfEventAction() and 
     B2EventAction::EndOfEventAction(). 
     Moreover, for the first 100 events and every 100 events thereafter 
     information about the number of stored trajectories in the event 
     is printed as well as the number of hits stored in the G4VHitsCollection. 
     
     The run number is printed at B2RunAction::BeginOfRunAction(), where the 
     G4RunManager is also informed how to SetRandomNumberStore for storing 
     initial random number seeds per run or per event.         

 6- USER LIMITS
 
    This example also illustrates how to introduce tracking constraints
    like maximum step length, minimum kinetic energy etc. via the G4UserLimits
    class and associated G4StepLimiter and G4UserSpecialCuts processes.    
    See B2aDetectorConstruction (or B2bDetectorConstruction).

    The maximum step limit in the tracker region can be set by the interactive
    command (see B2aDetectorMessenger, B2bDetectorMessenger classes). 
    For example:

    /B2/det/stepMax 1.0 mm

 7- DETECTOR RESPONSE
 
     A HIT is a step per step record of all the information needed to
     simulate and analyse the detector response.
 
     In this example the Tracker chambers are considered to be the detector.
     Therefore, the chambers are declared 'sensitive detectors' (SD) in
     the B2aDetectorConstruction (or B2bDetectorConstruction) class.
     They are associated with an instance of the B2TrackerSD class.
 
     Then, a Hit is defined as a set of 4 informations per step, inside
     the chambers, namely:
        - the track identifier (an integer),
	- the chamber number,
 	- the total energy deposit in this step, and
 	- the position of the energy deposit.

     A given hit is an instance of the class B2TrackerHit which is created
     during the tracking of a particle, step by step, in the method
     B2TrackerSD::ProcessHits(). This hit is inserted in a HitsCollection. 

     The HitsCollection is printed at the end of each event (via the method
     B2TrackerSD::EndOfEvent()), under the control of the command:
     /hits/verbose 2

 The following paragraphs are common to all basic examples

 A- VISUALISATION

   The visualization manager is set via the G4VisExecutive class
   in the main() function in exampleB2a.cc (or exampleB2b.cc).    
   The initialisation of the drawing is done via a set of /vis/ commands
   in the macro vis.mac. This macro is automatically read from
   the main function when the example is used in interactive running mode.

   By default, vis.mac opens an OpenGL viewer (/vis/open OGL).
   The user can change the initial viewer by commenting out this line
   and instead uncommenting one of the other /vis/open statements, such as
   HepRepFile or DAWNFILE (which produce files that can be viewed with the
   HepRApp and DAWN viewers, respectively).  Note that one can always
   open new viewers at any time from the command line.  For example, if
   you already have a view in, say, an OpenGL window with a name
   "viewer-0", then
      /vis/open DAWNFILE
   then to get the same view
      /vis/viewer/copyView viewer-0
   or to get the same view *plus* scene-modifications
      /vis/viewer/set/all viewer-0
   then to see the result
      /vis/viewer/flush

   The DAWNFILE, HepRepFile drivers are always available
   (since they require no external libraries), but the OGL driver requires
   that the Geant4 libraries have been built with the OpenGL option.

   For more information on visualization, including information on how to
   install and run DAWN, OpenGL and HepRApp, see the visualization tutorials,
   for example,
   http://geant4.slac.stanford.edu/Presentations/vis/G4[VIS]Tutorial/G4[VIS]Tutorial.html
   (where [VIS] can be replaced by DAWN, OpenGL and HepRApp)

   The tracks are automatically drawn at the end of each event, accumulated
   for all events and erased at the beginning of the next run.

 B- USER INTERFACES
 
   The user command interface is set via the G4UIExecutive class
   in the main() function in exampleB2a.cc 
   The selection of the user command interface is then done automatically 
   according to the Geant4 configuration or it can be done explicitly via 
   the third argument of the G4UIExecutive constructor (see exampleB4a.cc). 
 
 C- HOW TO RUN
 
    - Execute exampleB2a in the 'interactive mode' with visualization
        % exampleB2a
      and type in the commands from run1.mac line by line:  
        Idle> /tracking/verbose 1 
        Idle> /run/beamOn 1
        Idle> ...
        Idle> exit
      or
        Idle> /control/execute run1.mac  or run2.mac 
        ....
        Idle> exit

    - Execute exampleB2a in the 'batch' mode from macro files 
      (without visualization)
        % exampleB2a run2.mac
        % exampleB2a exampleB2.in > exampleB2.out
 
