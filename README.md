# RevitJournalAbuser

Library contains 4 classes to abuse widely known loophole Journals replaying in Autodesk Revit.

If for some reason you never heard of it here's a quick rundown:
Each Revit session creates Journal file that logs every action that happens in this session. If you start new Revit session with such Journal passed as an argument, Revit will replay all actions from this Journal. Basically it's just a debug tool to recreate conditions that caused an error.
Luckily for us we can utilise this tool to execute various commands that are unavailable for us from API.
Under the hood Journal file use modified VB syntax.
