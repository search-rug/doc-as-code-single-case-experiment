# Document-as-code in the context of Interface-Control documents

A number of empirical studies suggest that serious integration and operational problems in large-scale systems/systems-of-systems are often linked to Interface Control Documents (ICDs) management. These empirical studies suggest that interfaces are in many cases incomplete or unclearly defined, especially between hardware and software subsystems, that, together with the domain knowledge gap between the owners and the users of the interface, often lead to misunderstandings or potentially false assumptions. Furthermore, the development of the software artifacts derived from them (e.g., simulations, tests) are error-prone because the ICDs, by being human-readable-only, require manual transcription. This also creates a problem of redundant specifications scattered across multiple artifacts ---both the human-readable document and the artifacts derived from them--- and hence the risk of having them out-of-sync with each other. 

With this motivation, an action research study is being carried on in the context of LOFAR, with the goal of identifying and evaulating alternative ICD management approaches that could improve the aforementioned issues.

In the particular case of LOFAR, the ICDs have been managed in word-processing documents under version control, having recently switched to a corporate-based wikis for this purpose. Given this context, during the diagnosing phase of the action research study, the following details on the aforementioned issues were identified:


## Interface documentation-related issues

- The ICDs often contains terminology that was clear for the people involved in its original version, but years later it could be interpreted differently. This problem is exacerbated by the fact that hardware and software engineers use similar terminology with different interpretations from each one’s domain.
- Some trivial, but critical elements are often omitted on the ICDs. This could lead to risky assumptions, or to error-prone informal information exchange to fill the details gap.
- Badly written definitions due to the lack of English proficiency of the writers, or misinterpretation of existing definitions due to the same language-proficiency issues.
- Time-behavioral and state-related aspects of the interfaces are often not included in the ICDs. 

## Interface documentation management-related issues

- Changes on the ICDs are not announced but rather discovered by people working with them.
- There is a need for the right balance of documentation maintenance efforts and the actual engineering/development ones. Currently, information is duplicated across ICDs, the implemented artifacts, and other tools created to support the development process.

## Study design

In the action planning phase of the first cycle of the study, a documentation management approach inspired on the document-as-code philosophy, i.e., managing documentation the same way software source code is managed in modern settings, has been proposed. This first cycle is particularly aimed at exploring the first two of the aforementioned documentation-related issues, and the documentation-managament ones. The remaining ones ---language-proficiency related, and the lack of dynamic behavior details--- will be explored in the following cycle based on the findings of this evaulation exercise. Given the above, this documentation management approach includes the following elements:


1. In order to make the technical documentation (in this case, ICDs) compatible with modern software-centered versioning control systems and automation platforms, a machine-readable, text-based markup language for the writing process.

2. A Continuous-Integration/Continuous-Delivery pipeline, commonly used in a software setting to automate the building and quality assessment of source code, is tailored to the context of technical documentation. With this, the organization can define policies for documentation quality enforcement before an ICD is published.

3. The integration of one or more formalisms for the specification of technical elements wihtin the ICDs (e.g., hardware descriptions) is allowed. With this, and a number of automatic transformations applied to them for content or software artifacts generation, the ICDs are expected to become the single-source-of truth during subsystem interface development or maintenance.

4. A centralized management of the documents created under this document-as-code environment is put into place, so the documents and the dependencies between their different versions (e.g., when an ICD refers to a particular version of the previous one) can be tracked. With this, the people whose work depend on these documents can be aware (or notified about) when an update is taking place.

In the following exercise, you will perform a test-drive, in a set of scenarios, of a working prototype of the documentation pipeline described above. 


### Scenario one - Document versioning and basic ICDs publication/tracking

In the proposed documentation management approach, each ICD is written in a markup language and maintained on its own Git repository. In this exercise, you will test-drive two artifacts: (1) a docker container that build and test (against the predefined 'quality gates') these documents within a CI/CD pipeline, and (2) a document-centered platform that keep track of the status of the overall documentation.

Steps:

1. Create a new public, empty repository on Gitlab (https://www.gitlab.com), as the tools have not been tested on private Gitlab configurations. Choose a name that you think would be meaninful for a technical document.

2. Use the [following document](http://) as a template for your first (and rather small) ICD. Make sure you the file is in the root of the repository, and that it keeps the 'index.adoc' name. Add this document to your repository. Add some content or diagramas.

3. To integrate your repository into the centralized documentation pipeline, first enable the "Protected Tags" to the ones with the pattern v* (allowing developers and maintainers to create them). Then set the variable BACKEND_CREDENTIALS (with the values sent to you by email), with the flags 'Protect variable' and 'Mask variable' enabled.

![](protected_tag.png)

![](add_variable.gif)

4. Create a .gitlab-ci.yml (with the '.' at the beginning) at the root of your local repository if it doesnt exist yet. Set the following YAML code as its (only) content:

```yml
include:
  - remote: https://gitlab.com/hcadavid/dac-pipeline-configuration/-/raw/main/cicdconfig.yml
```

5. Once you commit and push the above changes, a CI/CD job should be launched. The compiled document should be now accessible in gitlab pages at <your_user_name>.gitlab.io/stage. This 'staging' version of the document, which won't be considered as an official one, would be useful for reviews before posting an actual version of the document.

6. Now, add more content to what will be the first official version of the document. In order publish the document as such, commit the changes and create an annotated tag. Given that the messages on the annotated tags would be used for automatically generate the __Document history__ section of the document, write one that would work for this purpose, e.g.:

```
$ git tag -a v0.1 -m "Draft version ... "
```

7. Push the tag to the gitlab repository. This time, as you are pushing a concrete version of the document, the building and validation process will exchange information with the document management system to start keep tracking of this and future versions. Once the document has been built and published, its information should be now available on the [documentation management dashboard](https://documentation-dashboard.herokuapp.com/). Use the same user/name credentials sent to your email for authentication.

8. Create a second repository for a different ICD (you will make references between them in one of the following scenarios) and publish a version of it. You can just duplicate the previous one and change part of the content, just do not forget to add the BACKEND_CREDENTIALS variable and enable the protected tags.

8. If you are able to check the details of both documents, and their status is PUBLISHED, you are done with the basic publication scenario. Otherwise, please double check the previous steps or get in touch with the researchers for assistance). 


### Scenario two - Quality gates

The concept of 'quality gate' refers to the acceptance criteria a project must meet before proceeding to the follow-up delivery phases. In software, automatically verifiable criteria are integrated into the CI/CD environment with this purpose. The proposed documentation approach would enforce two main criteria related to the issues dicussed at the beginning of the document: prose quality/clarity, and completeness of expected technical details provided. In this proof of concept, two 'quality gates' are included for illustrative purposes: (1) acronmyms must be explicitly defined by linking them to an entry in a centralized glossary, and (2) the models used in the document to describe hardware elements must explicitly define the endianness of the registers. For the latter, you will use a prelimiary version of an asciidoc extension that allows to embed hardware definitions in SystemRDL language (it is not necessary to have to previous knowledge about its syntax, as the exercise will guide you on how to modify it). 

Steps:

1. Add a SystemRDL model. The following one represents a registers map for a device called 'turboencarbulator'. In a nutshell, it describes a register map (REG1) with two fields (f1 and f2) of 8 and 16 bits respectively, and a default value of 256 for the former. Note that the dashes (----) are not part of SystemRDL defintion, but the way Asciidoc defines the beginning and the end of the [systemrdl] macro. Pretend that you overlooked the inconsistency of storing 256 in a 8-bit field, and copy the definition as-is in the first document created on the previous scenario.


```
[systemrdl, name="turboencarbulator"]
-----
addrmap tiny {
    reg {
        field {
 	        name="reg_aa";
            sw=rw;
            hw=r;
        } f1[8] = 256;

        field {
 	        name="reg_bb";
            sw=r;
            hw=w;
        } f2[16];
    }REG1@0x2D;
};
-----
```

2. In the same document, add a sentence on the ICD that includes the acronym 'KSP'. Commit these two changes, create a new version tag, and push it on the repository. This version  has one error -the inconsistency of on the default value of the registry field- and to conflicts with quality gates, as the registry map doesn't define the endiannes, and an acronym is not 

Check the way the pipeline has two types of documentation publication failures: consistency errors, and failed 'quality gates': 


2. However, this definition has two issues: the default value for f1, 256, doesn't fit on the 8 bits of the field. Second, it doesn't follow one of the quality gates: endianness must be always defined on a register map.


- Error: internal inconsistencies in the SystemRDL model.
- Failed Quality gates: an optional SystemRDL element (in this case endianness), that was set as mandatory in this context, was missed; an acronym was included but not described in the document.

3. Fix the inconsistencies:
    - Add the following elements to the SystemRDL specification:
    ```
     To be defined
    ```
    
    - Add the macro glossary:default[] in the section where you want the glossary to be populated. 
    - Go to the dashboard, select Glossaries, and look for the acronym. There you can see how you can refer to the acronym within the document in a way its definition is automatically included in the document's glossary.

4. Commit, set a new version tag, and push. Look at the generated elements, namely: a human-readable representation of the registry, and the C headers.


### Scenario three - ICDs as the single source of truth, centralized versions/dependencies tracking.

This scenario illustrates how having the ICDs as the single source of truth (by making them machine-readable), and keeping track of dependencies between documents, would enable features to assist in the prevention of these outdatedness-related issues. The chosen platforms/tools  (C++, CMake) are for illustrative purposes only, and the example is minimalist for the sake of simplicity of the exercise. Here you will (1) create a simple codebase that makes use of the information given by the document created in the previous scenario, and (2) add a new document that makes reference to this document.


1. Clone the C++ codebase from [this repository](http://to_be_defined).
2. Open the document previously published (on its deployment site). Go to the section where the register map was defined. Copy the header URL.

![](copy-header-url.gif)

3. Pull the header inside the project, and add it in the CMake file.
```
wget [url]
``` 
3. In the document, copy the URL of the header's checksum, add it in your CMake file as follows. If you don't have CMake installed, do this in this containerized environment:

```C
cmake_minimum_required(VERSION 3.15)

project(header_check_example)

file(DOWNLOAD CHECKSUM_URL ${CMAKE_CURRENT_BINARY_DIR}/checksum)
file(STRINGS ${CMAKE_CURRENT_BINARY_DIR}/checksum updated_checksum)
file(SHA256 ./turboencarbulator.hpp current_bytecode)

if (NOT ${updated_checksum} STREQUAL ${current_bytecode})
        message("Warning: the header of turboencarbulator.hpp might not be up-to-date with respect to the ICD it was based on")
else()
        message("Checksum check... OK")
endif()

add_executable(app main.cpp)

```

4. Edit the [ICD A2](http://to_be_defined), and add a reference to the previously created one. To do so, go to the dashboard, find the previously created document and copy the AsciiDoc macro used for referencing other documents:

![copying-docref-macro.gif](copying-docref-macro.gif)

2. Commit, add a new version tag, and push it (including the tag). Check the status of both documents on the Dashboard (both should be PUBLISHED)

3. Now, let's assume a change is made to the original document (the one referenced by the new document, and whose content was used in the codebase). Let's modify the SystemRDL specification of the 'turboencarbulator' so that reg_aa is now 'read only' for software.

4. Commit, add a new tag version and push the changes. Check the new status of the ICD A2, and the information available for a person to analyze how to deal with the scenario of working with a document that might require being revisited.
5. Returning to the developer perspective: go to the development environment and, re-run the build process and see difference on the output after the updates on the ICD.



