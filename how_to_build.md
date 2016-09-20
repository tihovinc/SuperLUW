#### Build steps:

1. Download and install Visual Studio 2015 Comunity Edition
2. Download and install Cmake for Windows
3. Download and install Intel MKL
4. Download and unpack SuperLU
5. Open CMD, call VS2015x64NativeCmdWithMKL.bat, cd into SuperLU folder and then type:

  mkdir build  
  cd build  
  cmake -D USE_XSDK_DEFAULTS=true -D BUILD_SHARED_LIBS=false -G "Visual Studio 14 2015 Win64" ..  

6. Open SuperLU.sln from build folder
7. Select all lowercase named projects (superlu, matgen, c_test, d_test, s_test, z_test)
8. Right click -> Properties
9. Select All Configurations in drop down control Configuration
10. Choose Configuration Properties -> General
11. Edit Output Directory field to contain $(SolutionDir)Out\$(Configuration)\
12. Edit Intermediate Directory field to contain $(SolutionDir)Int\$(Configuration)\$(ProjectName)\
13. If you want to be able to execute executables on Windows XP than change Platform Toolset to v140_xp
14. Choose Configuration Properties -> C/C++ -> Preprocessor
15. Add _CRT_SECURE_NO_WARNINGS to Preprocessor Definitions field
16. Choose Configuration Properties -> C/C++ -> Advanced
17. Add 4013;4101;4244;4305 to Disable Specific Warnings field
18. Close Properties (Ok button), select test projects only and right click -> Properties
19. Select All Configurations in drop down control Configuration
20. Choose Configuration Properties -> Linker -> General
21. Add $(OutDir) in front of Additional Library Directories field
22. Choose Configuration Properties -> Linker -> Input
23. In field Additional Dependencies remove everything except windows standard libraries and add superlu.lib;matgen.lib; and either :
	- add to Additional Dependencies: C:\Program Files (x86)\IntelSWTools\compilers_and_libraries\windows\mkl\lib\intel64\mkl_intel_lp64_dll.lib;C:\Program Files (x86)\IntelSWTools\compilers_and_libraries\windows\compiler\lib\intel64\libiomp5md.lib;C:\Program Files (x86)\IntelSWTools\compilers_and_libraries\windows\mkl\lib\intel64\mkl_intel_thread_dll.lib;C:\Program Files (x86)\IntelSWTools\compilers_and_libraries\windows\mkl\lib\intel64\mkl_core_dll.lib
	- open Configuration Properties -> Intel Performace Libraries and set drop down field Use Intel MKL to Parallel
24. Add getopt.c to matgen projects
25. Build BUILD_ALL project
26. Build TEST_ALL project  

If you need to build dlls than cd into out dir for current configuration and execute:

  dumpbin /linkermember:2 matgen.lib > matgen.def  
  dumpbin /linkermember:2 superlu.lib > superlu.def  
  
and edit out from def files everything except function names and add def header at the beginning of file (

  LIBRARY LibName  
  EXPORTS    
  
), copy def files into appropriate project folders, right click solution in VS, choose Configuration Manager,
from drop down control Active Solution Configuration select New and in new dialog box select in field
Copy Setting From configuration which you prefer and write appropriate name. Set new configuration as active,
select projects superlu and matgen, right click -> Properties -> General and change field Configuration Type to dll.
After that set up linker options for those projects similar to linker options for test projects, in inputs for linker
set for superlu only standard windows libraries and MKL, and for matgen the same + superlu. In Linker -> System
-> Subsystem set Windows and in C++ -> Preprocessor -> Preprocessor Definitions add _USRDLL.
