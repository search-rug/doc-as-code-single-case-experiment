# Document-as-code in the context of Interface-Control documents

A number of empirical studies suggest that serious integration and operational problems in large-scale systems/systems-of-systems are often linked to Interface Control Documents (ICDs) management. These empirical studies suggest that interfaces are in many cases incomplete or unclearly defined, especially between hardware and software subsystems, which, together with the domain knowledge gap between the owners and the users of the interface, often lead to misunderstandings or potentially false assumptions. Furthermore, the development of the software artifacts derived from them (e.g., simulations, tests) are error-prone because the ICDs, by being human-readable-only, require manual transcription. This also creates a problem of redundant specifications scattered across multiple artifacts ---both the human-readable document and the artifacts derived from them--- and hence the risk of having them out-of-sync with each other. 

With this motivation, an action research study is being carried on in the context of LOFAR, with the goal of identifying and evaluating alternative ICD management approaches that could improve the aforementioned issues.

In the particular case of LOFAR, the ICDs have been managed in word-processing documents under version control, having recently switched to a corporate-based wiki for this purpose. Given this context, during the diagnosing phase of the action research study, the following details on the aforementioned issues were identified:


## Interface documentation-related issues

- The ICDs often contain terminology that was clear for the people involved in its original version, but years later it could be interpreted differently. This problem is exacerbated by the fact that hardware and software engineers use similar terminology with different interpretations from each one’s domain.
- Some trivial, but critical elements are often omitted on the ICDs. This could lead to risky assumptions, or to error-prone informal information exchange to fill the details gap.
- Badly written definitions due to the lack of English proficiency of the writers, or misinterpretation of existing definitions due to the same language-proficiency issues.
- Time-behavioral and state-related aspects of the interfaces are often not included in the ICDs. 

## Interface documentation management-related issues

- Changes in the ICDs are not announced but rather discovered by people working with them.
- There is a need for the right balance of documentation maintenance efforts and the actual engineering/development ones. Currently, information is duplicated across ICDs, the implemented artifacts, and other tools created to support the development process.

## Study design

In the action planning phase of the first cycle of the study, a documentation management approach inspired on the document-as-code philosophy, i.e., managing documentation the same way software source code is managed in modern settings, has been proposed. This first cycle is particularly aimed at exploring the first two of the aforementioned documentation-related issues, and the documentation-management ones. The remaining ones ---language-proficiency related, and the lack of dynamic behavior details--- will be explored in the following cycle based on the findings of this evaluation exercise. Given the above, this documentation management approach includes the following elements:


1. In order to make the technical documentation (in this case, ICDs) compatible with modern software-centered versioning control systems and automation platforms, a machine-readable, text-based markup language for the writing process.

2. A Continuous-Integration/Continuous-Delivery pipeline, commonly used in a software setting to automate the building and quality assessment of source code, is tailored to the context of technical documentation. With this, the organization can define policies for documentation quality enforcement before an ICD is published.

3. The integration of one or more formalisms for the specification of technical elements within the ICDs (e.g., hardware descriptions) is allowed. With this, and a number of automatic transformations applied to them for content or software artifacts generation, the ICDs are expected to become the single-source-of truth during subsystem interface development or maintenance.

4. A centralized management of the documents created under this document-as-code environment is put into place, so the documents and the dependencies between their different versions (e.g., when an ICD refers to a particular version of the previous one) can be tracked. With this, the people whose work depends on these documents can be aware (or notified about) when an update is taking place.

In the following exercise, you will perform a test-drive, in a set of scenarios, of a working prototype of the documentation pipeline described above. 


### Scenario one - Document versioning and basic ICDs publication/tracking

Note: You can do this exercise online, using GitLab's web editor, or locally, installing an editor like [AsciidoctorFX](https://asciidocfx.com/) and using your local git client.

In the proposed documentation management approach, each ICD is written in a markup language and maintained on its own Git repository. In this exercise, you will test-drive two artifacts: (1) a docker container that builds and tests (against the predefined 'quality gates') these documents within a CI/CD pipeline, and (2) a document-centered platform that keeps track of the status of the overall documentation.

Steps:

1. Create on your Gitlab (https://www.gitlab.com) two public repositories for the documents you will be working on during this exercise (from now on, document A and document B). To do so, fork [this template repository](https://gitlab.com/hcadavid/document-repository-template) twice, changing both repositories' names to something you would use for an ICD document. Keep in mind that these forks now include (1) a basic index.adoc document to work on, and (2) a .gitlab-ci.yml file (Gitlab's CI configuration file) already configured to fire a job -upon a document update- with the tooling required to integrate the repository in the proposed documentation pipeline.

2. The aforementioned tooling requires authorization before they can interact with the documentation management system. To do so, first enable the "Protected Tags" to the ones with the pattern v* (allowing developers and maintainers to create them). Then set the variable BACKEND_CREDENTIALS (with the values sent to you by email), with the flags 'Protect variable' and 'Mask variable' enabled.

![](protected_tag.png)

![](add_variable.gif)

2. Add some content to the document A by cloning it on your computer and adding some content to them (so you can tell one from the other) using a local Asciidoc editor like [AsciidoctorFX](https://asciidocfx.com/), and committing/pushing the changes to the repository afterward. Alternatively, you can do the above using Gitlab's web editor.

3. Once you have committed and pushed the above changes, a CI/CD job should be launched on document A's repo. The compiled version of the document should be now be available in gitlab pages at <your_user_name>.gitlab.io/<repo_name>/stage. This 'staging' version of the document, which won't be considered as an official one, would be useful for reviews before posting an actual version of the document.

4. Now, add some more content to Document A, and make this the first 'official' version of it. To do so, commit the changes and create an annotated tag. Given that the messages on the annotated tags would be used to automatically generate the __Document history__ section of the document, write one that would work for this purpose, e.g.:

```
$ git tag -a v0.1 -m "Draft version ... "
(alternatively, create the new tag -with a comment- on Gitlab's web interface)
```

5. Push the tag to the gitlab repository. This time, as you are pushing a specific version of the document, the building and validation process this time will exchange information with the document management system to start to keep tracking of this and future versions. Once the document has been built and published, its information should be now available on the [documentation management dashboard](https://documentation-dashboard.herokuapp.com/). Use the same user/name credentials sent to your email for authentication. Open the last official version of the document by following the provided link in the documents list.

![](open_last_version.gif)

6. Now, do the same to Document B: add some content, commit it, create a version tag, and push it to the documents repository).

7. If you are able to check the details of both documents, and their status is PUBLISHED, you are done with the basic publication scenario. Otherwise, please double-check the previous steps or get in touch with the researchers for assistance). 


### Scenario two - Content generation and Quality gates

This scenario illustrates how the proposed ICDs management approach (1) would reduce redundant -and error-prone- writing/transcription efforts through content/code generation, and (2) how the overall quality of the documentation could be improved by enforcing quality and completeness criteria by means of 'quality gates' (also reducing hard feelings between writers and reviewers in the process).

Please note that this scenario assumes that a fully-fledged editing environment will be available so that technical writers will be able to evaluate their documents locally (and in real time), with the same rules and metrics, before submitting changes to the documentation pipeline.

The concept of 'quality gate' refers to the acceptance criteria a project must meet before proceeding to the follow-up delivery phases. In software, automatically verifiable criteria are integrated into the CI/CD environment for this purpose. The proposed documentation approach would enforce two main criteria, related to the issues previously discussed: writing quality/clarity, and completeness of the technical details provided. In this proof of concept, two simple 'quality gates' are enabled for illustrative purposes. (1) Writing style: the measurements of distance, temperature, volume, size, weight, etc, must always be described with numerals (as suggested by Microsoft's style guidelines). (2) The hardware descriptions embedded in the document must explicitly define the endianness of the registers. For the latter, you will use a preliminary version of an Asciidoc extension that allows technical writers to embed hardware specifications using SystemRDL, which will be automatically transformed into human-readable content and base software artifacts (e.g., C headers). It is not necessary to have previous knowledge on this language, as the exercise will guide you on how to modify it. 

Steps:

1. Add a SystemRDL model to your first document using the [systemrdl] macro. The following code snippet shows how to define a simple registry map within the document (larger models could be imported from external files).  In a nutshell, this model describes a register map (REG1) with two fields (f1 and f2) of 8 and 16 bits respectively, and a default value of 256 for the former. Note that the dashes (----) are not part of SystemRDL definition, but the way Asciidoc defines the beginning and the end of the inline-[systemrdl] macro. Pretend that you overlooked the inconsistency of storing 256 in an 8-bit field, and copy the definition as-is in the first document created on the previous scenario. *Note: if you are working with AsciidoctorFX, or any other editor, it won't render -locally- many of the commands used in this guide (including the [systemrdl] one), as they are custom-made ones. By now you need to build the document within the CI/CD pipeline to have  a full-rendered version of it (a fully fledged local editing environment is a work in progress).


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

2. In the system overview section of the ICD, add a sentence that includes the value of a unit of measure (weight, longitude, etc) spelled out, e.g.: ' It is expected a resolution of ten megapixels ' . Commit these two changes, create a new version tag, and push it on the repository. This version has one error -the inconsistency of the default value of the registry field- and two conflicts with quality gates, as the registry map doesn't define the endianness, and there are violations of a (hypothetical) writing style guideline. Open the [management dashboard](https://documentation-dashboard.herokuapp.com/) and check the new status of the document, and the information provided by the 'failed ICD builds' section on it.

![](errors-check.gif)

3. Fix the way the value of the unit of measure was described in the text. Change the size of the field 'f1' of the SystemRDL definition so that it can now hold the default 256 value, and define the endianness by adding __bigendian;__ or __littleendian__, e.g.,:

```
     addrmap tiny {
        littleendian;        
        reg {
            field {
            ...
```

5. Before committing the changes, add an acronym into the document to test the centralized glossaries management feature, and how it can automatically generate a glossary in the document (acronyms/abbreviations management features would be available in future versions). This only requires adding the 'acr' macro (acr:<acronym>[context=<context>]) in the document, but alternatively you can also search for terms and copy such a macro through the documentation management platform. To do so, in the [management dashboard](https://documentation-dashboard.herokuapp.com/) go to the Glossary section and search for an acronym (e.g., KSP). From there, copy the macro required to insert acronyms definitions in the document. Write a sentence that includes the acronym and use the macro within it accordingly. 

![](copy-glossary-ref.gif)

6. Below the 'Glossary' section of the document add the macro glossary::default[] (if it isn't there yet) so that the building process generates a table with the corresponding definitions.

7. Commit the changes, set a new version tag, and push it to the repository. Once the [documentation dashboard](https://documentation-dashboard.herokuapp.com/) shows the document as Published, open its last version. As you can see, the generated document now includes a human-readable representation of the SystemRDL specification. Furthermore, with the two buttons below it you can copy two URLs: one with a C header file that corresponds to the SystemRDL model, and another to its checksum. 

![](copy-header-url.gif)

8. If you can download both documents (e.g., with the wget or curl commands), you have successfully completed this second scenario. 
  

### Scenario three - ICDs as the single source of truth, centralized versions/dependencies tracking.


This scenario illustrates how the ICDs, by becoming the actual 'single source of truth' for interfacing-related activities, could be used to foresee potential issues as interfaces evolve over time. In particular, it shows how, by keeping track of the documents' lifecycle and dependencies between them, it could detect and notify when an ICD might require a revision (e.g., when one of the ICDs referenced by it has been updated), or when a software artifact derived form it (by means of code generation) is outdated. 

In this scenario you will assume the role of a developer, who will work with a codebase that will use the specifications given by the ICDs (the one with the register map definitions, from now on referred to as document A).   

1. Clone the C/CMake codebase from [this repository](https://gitlab.com/hcadavid/icd-aware-build-script) in a local directory (a different one from the one where the sources of the documents are). This codebase depends on the headers generated by the ICD, so go to the document and use the button below the register map (Copy header's file location) to copy the URL where the header corresponding to such register map is located. Use wget or curl to download the header in the directory of the codebase (according to the previous steps, it should be called 'turboencarbulator.h'.

![](copy-header-url.gif)

2. Edit the CMakeList.txt file. As you can see, this script (besides building the make file), checks a SHA-256-checksum of the header against an online checksum (the one of the most recent available online). To make the versioning self-checking work, you just need to set the value of the variable TURBOENCARBULATOR_LATEST_CHECKSUM accordingly. You can copy the URL of the most recent header's checksum with the other button below the registers map (Copy header's checksum file location).


```

cmake_minimum_required(VERSION 3.15)

project(online_header_check_example)

set(TURBOENCARBULATOR_LATEST_CHECKSUM <Checksum's URL copied from the ICD>)

...

```

3. Compile and execute the program. Make sure the process report that the header is up-to-date.

```
$ cmake .
$ make
$ ./app
```

4. Edit the second document created in the first scenario (from now on document B), and add a reference to document A. To do so, go to the dashboard, find the Document A and copy the AsciiDoc macro required for referencing documents ([docref:<doc_name>[version=<doc_version>]]). Insert this macro within a sentence in document B.

![copying-docref-macro.gif](copying-docref-macro.gif)

5. Commit, add a new version tag, and push it (including the tag). Check the status of both documents on the Dashboard (both should be PUBLISHED).

6. Now, make changes to the content of Document A, and modify the SystemRDL specification in it so that reg_aa is now 'read only' for software, and the default value is 0.

7. Commit, add a new tag version and push the changes. Check the new status of Document B in the dashboard. Once the new version is published, the system should show Document B with a warning status, as it references an old version of Document A. Run, once again the CMake build of the project. If the process warns you about an outdated header, you are done with Scenario #3.
\





***Thank you for your participation in this exercise!*** This exercise presented a working proof of concept that resulted from the first cycle of an action research study. Your feedback is really important to improve/adjust the overall  documentation management approach and its related artifacts (before turning them into proper open source software projects) for the following cycle of the study. Given this, we would really appreciate it if you could fill out [this survey](https://rug.eu.qualtrics.com/jfe/form/SV_8okI7KLnEEWkaiy). Thank you again for your time and consideration.






