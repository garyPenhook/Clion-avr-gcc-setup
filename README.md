README: Setting Up a Custom Compiler in CLion
This guide provides instructions for configuring a custom compiler in CLion using a YAML file. This is useful for projects that use specific, rarely used, or proprietary compilers not natively supported by CLion.

Prerequisites
CLion IDE installed on your system.
Custom Compiler details, including:
Supported languages.
Compiler binary names.
Include directories for standard headers.
Predefined macros.
Steps to Configure a Custom Compiler
1. Create a YAML File
The YAML file should describe the compiler configuration.
Each section in the YAML file must include:
Description: A brief description of the compiler.
Matching Records: Define how CLion identifies the compiler.
Informational Records: Provide compiler-specific data.
2. Matching Records
Define how CLion matches the compiler using:

match-compiler-exe: A regex to match the compiler binary name (e.g., "(.*/)?sdcc(.exe)?").
match-source: A regex to match source file names (e.g., ".*\\.c" for C files).
match-args: Command-line arguments or switches (e.g., "-mpic16").
match-language: Specify the language (C or CPP).
3. Informational Records
Provide the following:

Include Directories: Paths to standard header files.
Predefined Macros: Compiler-specific macros.
Example: Small Device C Compiler (SDCC)
Compiler Details
Language: C
Binary Name: sdcc or sdcc.exe
Mandatory Arguments: -mpic16 and --use-non-free
Header Paths: Use --print-search-dirs to list directories.
Predefined Macros: Use -E and -dM with an empty source file.
Project Setup
Create a project folder with:
A Makefile.
A main.c file.
A YAML file for the compiler configuration.
Minimal Makefile:
makefile
Copy Code
clean:
all: main.c
    sdcc -mpic16 --use-non-free -S main.c
main.c Example:
c
Copy Code
#include <stdio.h>
int main() {
    printf("Hello, World!\n");
    return 0;
}
int __code i = 0;
#ifndef __SDCC_pic16
#  error "__SDCC_pic16 macro is expected to be defined"
#endif
YAML File Stub:
yaml
Copy Code
compilers:
  - description: SDCC for PIC-16
    match-compiler-exe: "(.*/)?sdcc(.exe)?"
Using the Custom Compiler in CLion
Open Settings/Preferences > Build, Execution, Deployment > Toolchains > Custom Compiler.
Enable Use custom compiler config and select the YAML file.
Reload the project via Tools > Makefile > Reload Makefile Project.
Collecting Missing Compiler Information
Add the following to the Makefile:
makefile
Copy Code
gather_info:
    sdcc -mpic16 --use-non-free --print-search-dirs
    echo //void > void.c
    sdcc -mpic16 --use-non-free -E -dM void.c
Run the gather_info target to collect:
Include Directories: Add them as relative paths in the YAML file.
Predefined Macros: Add them under defines-text in the YAML file.
Final YAML File Example
yaml
Copy Code
compilers:
  - description: SDCC for PIC-16
    match-compiler-exe: "(.*/)?sdcc(.exe)?"
    match-args: -mpic16
    match-language: C
    include-dirs:
      - ${compiler-exe-dir}/../include/pic16
      - ${compiler-exe-dir}/../include
      - ${compiler-exe-dir}/../non-free/include/pic16
      - ${compiler-exe-dir}/../non-free/include
    defines-text: "
    #define __SDCC_USE_NON_FREE 1
    #define __SDCC_PIC18F452 1
    #define __18f452 1
    #define __STACK_MODEL_SMALL 1
    #define __SDCC_pic16 1
    #define __SDCC_ALL_CALLEE_SAVES 1
    #define __STDC_VERSION__ 201112L
    #define __STDC_HOSTED__ 0
    #define __SDCCCALL 0
    #define __STDC_UTF_16__ 1
    #define __SDCC_VERSION_MINOR 2
    #define __STDC_ISO_10646__ 201409L
    #define __SDCC_VERSION_PATCH 0
    #define __SDCC_VERSION_MAJOR 4
    #define __STDC_NO_VLA__ 1
    #define __SDCC 4_2_0
    #define __STDC_UTF_32__ 1
    #define __STDC_NO_THREADS__ 1
    #define __SDCC_CHAR_UNSIGNED 1
    #define __STDC_NO_ATOMICS__ 1
    #define __STDC__ 1
    #define __SDCC_REVISION 13081
    #define __STDC_NO_COMPLEX__ 1

    #define __interrupt
    #define __code
    #define __at
    "
Verifying the Configuration
Open main.c in the editor.
Use Help > Diagnostics Tools > Show Compiler Info to verify:
Compiler kind: Custom Defined Compiler.
Compiler description: SDCC for PIC-16.
Reload the project and ensure no errors in main.c.
Additional Notes
Ensure the match-compiler-exe regex is accurate.
Use relative paths for portability.
Test the configuration thoroughly.
Enjoy using your custom compiler in CLion!
