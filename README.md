<h1>File Integrity Monitor</h1>

<h2>Description</h2>

I utilized Windows PowerShell to write a script that would be used by, in my hypothetical situation, the Imperial Army (from the Star Wars movie series), to monitor their beloved Death Star's external and internal "security capabilities" related documents.  

Included within these files, is a reference to the infamous "Thermal Exhaust Port", that was ultimately exploited by the Jedi and Rebellion forces and led to the complete destruction of the Imperial Army's 1st Death Star.
<br />
<br />
<img src="https://imgur.com/PXSQFJw.png" height="60%" width="60%" alt="File Integrity Monitor"/>
<br />


<h2>Utilities and Environments Used</h2>

<br />
<b>PowerShell ISE</b>
<img src="https://imgur.com/8fxvrrL.png" height="40%" width="40%" alt="Windows AD within a VM"/>
<br />
<br />
<b>Windows 10</b> (21H2)
<br />
<br />
<br />
<h2>Project walk-through:</h2>

<p align="Left">
<b>Windows PowerShell ISE - 
Base script and functions creation:<b> <br/>
<br /> 
- This script, when it is run, will prompt user to make a choice, either:

     A - To establish a new Baseline from which to reference (is a pre-requisite prior to selecting option "B".)

     B - To begin monitoring targeted files within a specified folder, against the previously saved Baseline, for deviations to file hashes or new/deleted files. 

          (the monitoring occurs every 5 seconds in this example, detailed in an image of the script further below)  
<br />
<br />
- Line 13 - Once the Baseline has been created, function "Calculate-File-Hash" creates a SHA512 hash for each file that exists in the target folder.  

- Line 18 - The current Baseline is overwritten with a new one by re-running the script and selecting option "A" again (this is the purpose of the "Overwrite_Baseline_If_Already_Exists" function
<br />
<img src="https://imgur.com/O3jkDoN.png" height="120%" width="120%" alt="File Integrity Monitor"/>
<br />
<br />
<br />
<br />
<b>Optional "email alerts" script <b>  <br/>
<br />
- This portion of the script is to showcase what it would look like to have email alerts active.

There are 2 different types of email alerts 

This specific email message will be sent:
- If the scripts detects a new file that does not have a corresponding hash (a newly created file that has not been vetted yet, hence a hash was not created)
- or if the hash of an existing file does not match with the known (stored) hash of that existing file (the file was modified in some way)
<img src="https://imgur.com/0Hezj2v.png" height="120%" width="120%" alt="File Integrity Monitor"/>
<br />
<br />
This specific email will be sent If, during the latest sweep, a file and its "hash" is now missing, when reconciled against the Baseline.
<img src="https://imgur.com/thZZy1V.png" height="120%" width="120%" alt="File Integrity Monitor"/>
<br />
<br />
<br />
<br />
<b>Script for option "A"<b> <br/>
<br />
- Line 61: Selecting this option, will overwrite the existing Baseline data

- Line 66 creates object "$files" which finds all the files within the stated file path (in this case C:\Users\....\desktop\FIM_files\files).

- Line 70: structures the "$hash" to calculate a file has associated with each file (by its path known as "full name"), and outputs those results in a new text file titled "baseline.txt" 

- Line 75: outputs on the command line for user to see, the text line "Calculate new hashes, make new baseline.txt"

*Note that option A was entered, and the command line output lists all the file names
An image of the Baseline.txt is shown further below

<br />
<img src="https://imgur.com/hX9XuWO.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<br />
<br />
<br />
<br />
<b>Script for option "B"<b>

- Line 80: Outputs on the command line for user to see "Read existing baseline.txt, start monitoring files"

- Line 66: Creates empty dictionary "$fileHashDict", that will be used soon

- Line 85: Creates object "$filePathsAndHashes", that pulls in the data from the "baseline.txt", while leaving the original data intact

- Line 87: Runs a "foreach" loop that takes data from "$filePathsAndHashes" and populates the file's name and its respective hash into "$fileHashDict", recognized as split into ".path" (0) aka KEY, and ".hash" (1) aka VALUE.

- Line 93: Begins a "True" loop<b>  <br/>

- Line 95: Sets the "Start-Sleep" timer at 5 seconds.  Every 5 seconds, the "$files" object pulls the data from the "files" folder

- Line 101: "$hash" is run to calculate hashes for each file within the "files' folder

- Line 108: An "If" argument that checks If the "$fileHashDict" dictionary has any new ".path" data (will inherently not have  ".hash"), then this means a new file was created, since its respective ".hash" was not saved during the last time the "establish a Baseline" selection was made

  --This will prompt the alert "$($hash.Path) has been added!" identifying that specific "$hash.Path" and sending out the corresponding email per code lines 26-40

- Line 116: Is an "Else" argument that seeks to close the "IF" argument that was opened in Line 108.

- Line 120: Is an "If" argument that seeks to verify that no files have been modified (checks if contents of "$hash.path] &  $($hash.Hash))" equal (or match) the contents of "$fileHashDict"

- Line 125: Is an  "Else" argument that seeks to close the "if" argument opened in line 120.  If the "if" argument from line 120 is not fulfilled, then there was a deviation to the "$hash" and the file is likely compromised

  --This will prompt the "$($hash.Path) has changed!" alert to appear to user, and send the corresponding email per code lines 26-40

- Line 134: Runs a "foreach" loop that checks if there is a missing KEY as referred to in Line 87.  If a KEY is missing it means that a file was deleted.

  --This will prompt the "$($key) has been deleted!" alert to appear to user, and send the corresponding email per code lines 45 -56
<br />
 - This is an example of an event log that highlights the relevant Rule/Requirement Code ID # 
<br />
<img src="https://imgur.com/Q5vZRMB.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<br />
<br />
<br />
<br />
<b>The 4 files that are being monitored and their hashes<b>  <br/>
<br />
The files themselves, were stored "C:\Users\....\desktop\FIM_files\files" (shown to the right)

The hashes, for purposes of this lab, were stored one level up "C:\Users\....\desktop\FIM_files", to simulate them being stored elsewhere in periodic intervals based on the business needs, for increased security
<br />

<img src="https://imgur.com/PNiN01j.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<img src="https://imgur.com/kV5Dkie.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<img src="https://imgur.com/UrMSinS.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<img src="https://imgur.com/shUSshD.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<img src="https://imgur.com/adL83kG.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<img src="https://imgur.com/zB9RHqQ.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<br />
<br />
<br />
<br />
<b>Command line output when I added a new file to "C:\Users\....\desktop\FIM_files\files"<b>  <br/>
<br />

<img src="https://imgur.com/i0hExXi.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<br />
<br />
<br />
<br />
<b>Command line output when I added a "space" to file: D_Death_Star_Critical_Systems_That_May_Cause_Catastropic_Loss.txt<b>  <br/>
<br />

<img src="https://imgur.com/939A1CF.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<br />
<br />
<br />
<br />
<b>Command line output when I deleted file:
A_Death_Star_Exterior_Defenses.txt<b>  <br/>
<br />

<img src="https://imgur.com/FyYRI6s.png" height="100%" width="100%" alt="File Integrity Monitor"/>
<br />
<br />
<br />
<br />


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
