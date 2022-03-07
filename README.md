# Document-as-code in the context of Interface-Control documents

A number of empirical studies suggest that serious integration and operational problems in large-scale systems/systems-of-systems are often linked to Interface-Control documents (ICDs) management. These empirical studies suggested that interfaces are in many cases incomplete or unclearly defined, especially between hardware and software subsystems, which, together with the domain knowledge gap between the owners and the users of the interface, often lead to misunderstandings or potentially false assumptions. Furthermore, the development of the software artifacts derived from them (e.g., simulations, tests) are error-prone not only because the ICDs, by being human-readable-only require manual transcription. Furthermore, this this creates a problem of redundant specifications scattered across multiple artifacts -the human-readable document and the artifacts derived from them-, and hence the risk of having them out-of-sync with each other. With this motivation, an action research study is being carried on in the context of LOFAR, with the goal of identifying and evaulating alternative ICDs management approaches that could improve the aforementioned issues.

In the particular case of LOFAR, the ICDs, and their life-cycle related information (changelogs, discussions about future changes, etc) have been managed in word-processing documents under version control, having recently switched to a corporate-based wikis for this purpose. During the diagnosing phase of the action research study, the following details on the aforementioned issues were identified:

## Interface documentation-related issues

- Language consistency/uniformity: an ICD often contains terminology that was clear for the people involved in its original version, but years later it could be interpreted differently. This problem is exacerbated by the fact that hardware and software engineers use similar terminology with different interpretations from each one’s domain.
- Establishing fundamentals: some trivial, but critical elements are often omitted on the ICDs. This could lead to risky assumptions, or to error-prone informal information exchange to fill the details gap.
- Badly written definitions due to the lack of English proficiency of the writers, or misinterpretation of existing definitions due to the same language-proficiency issues.
- Time-behavioral and state-related aspects of the interfaces are often not included in the ICDs. 

## Interface documentation management-related issues

- Changes on the ICDs are not announced but rather discovered by people working with them.
- There is a need for the right balance of documentation maintenance efforts and the actual engineering/development ones. Currently, information is duplicated across ICDs, the implemented artifacts, and other tools created to support the development process.

In the action planning phase of the first cycle of the study, a documentation management approach inspired on the document-as-code philosophy, i.e., managing documentation the same way software source code is managed in modern settings, has been proposed with the following elements:

1. In order remove dependencies between technical documentation (in this case, ICDs) and propietary authoring software, making said documents compatible with modern software-centered versioning control systems, a machine-readable, text-based markup language will be used.

2. A Continuous-Integration/Continuous-Delivery pipeline, commonly use in a software setting to automate the building and quality assessment of the documents. With this, the organization can define policies for documentation quality enforcement before it us published.

3. The integration of one or more formalisms for the specification of technical elements of the ICDs (e.g., hardware descriptions) within the document. With this, and an number of automatic transformations applied to them for content or software artifacts generation, the ICDs are expected to become the single-source-of truth during subsysems-interfaces development or maintinance.

4. A centralized management of the documents created under this docs-as-code environment, so the documents and the dependencies between their different versions (e.g., when an ICD refers to a particular version of the previous one) can be tracked. With this, the people whose work depend on these documents can be aware (or notified about) when an update is taking place.

In the following exercise, you will perform a test-drive, in a set of scenarios, of a working prototype of the documentation pipeline above described. 


# Scenario one - Document versioning and review process

In this scenario, you will play the role of a technical writer, who is going to add the final changes to a work-in-progress ICD, candidate to be published as its first official version. In the proposed documentation management approach, the documents are stored in a Git repository with CI/CD settings that upon a document update, will build the document, and publishes it if it fullfills the expected quality characteristics. However, to add a human review to the process, by default the building process will publish a non-official version of the document on an 'staging' environment. Once the input has been provided (by informal means, or explicitly through pull requests), an official version can be published by adding a version tag.

Steps:

1. Clone this repositor in your workstation, and edit the index.adoc file, an Asciidoc file that already contains a basic template of a (very small) ICD.
2. Add content to the ICD, commit and push them into the repository.
3. Open the [documentation dashboard](http://tobedepplyed.com), authenticate with the credentials sent to you by email. Once the documents have been generated, your document should be listed as either ___NOT PUBLISHED - FAILED REVIEW SUBMISSION___ or ___NOT PUBLISHED - NEW STAGING VERSION___.

In the first case, there was an error. Check the details on the dashboard (add gif here). In the latter, check and open the URL of the staging version (for sharing with the involved people).

Now add changes, commit them, and tag the current version. Check in the dashboard that the status is PUBLISHED. Check the link where the official version should be created, and that your tag is included as a description.


# Scenario two - Quality gates

The concept of 'quality gate' refers to the acceptance criteria a project must meet before proceeding to the follow-up delivery phases. In software, automatically verifiable criteria are integrated into the CI/CD environment with this purpose. In the context of docs-as-code for the proposed ICDs management approach, two main criteria can be enforced: prose quality/clarity, and completeness of technical information. In this proof of concept, two 'quality gates' are includes for illustrative purposes: (1) acronmyms must be explicitly defined, a referred to an entry in a centralized glossary, and (2) the models used in the document to describe hardware elements must explicitly define the endianness of the registers. For the latter, the prototype supports hardware specifications using a SystemRDL specification (it is not necessary to have to previous knowledge about it syntax, as the exercise will guide on how to modify it). 

Steps:

1. Add a SystemRDL model. The following one represents a registers map for a device called 'turboencarbulator'. However, it has an issue, as the addresses overlap, and the endianness is not defined:


```
[systemrdl, name="turboencarbulator"]
-----
addrmap tiny {
    reg {
        field {
 	    name="reg_aa";
            sw=rw;
            hw=r;
        } f1[8] = 123;

        field {
            sw=r;
            hw=w;
        } f2[16];
    }r1;
};
-----
```

2. Add a sentence on the ICD, that uses the acronym KFCA. Test the document locally, or commit/add a new version tag. Check the way the popiline has two types of documentation publication failures: consistency errors, and failed 'quality gates': 

- Error: internal inconsistencies in the SystemRDL model.
- Failed Quality gates: an optional SystemRDL element (in this case endianness), that was set as mandatory in this context, was miseed; an acronym was included but not described in the document.

3. Fix the inconsistencies:
    - Add the following elements to the SystemRDL specification:
    ```
     To be defined
    ```
    

    - Add the macro glossary:default[] in the section where you want the glossary to be populated. 
    - Go to the dashboard, select Glossaries, and look for the acronym. There you can see how you can refer to the acronym within the document in a way its definition is automatically included in the document's glossary.

4. Commit, set a new version tag, and push. Look at the generated elements, namely: a human-readable representation of the registry, and the C headers.


# Scenario three - ICDs as the single source of truth, centralized versions/dependencies tracking.

This scenario illustrates how, having the ICDs as the single source of truth (by making them machine-readable), and keeping track of dependencies between documents, would enable features to assist in the prevention of these outdatedness-related issues. The chosen platforms/tools are only for illustrative purposes (C++, CMake), and the example is minimalist for the sake of simplicity of the exercise. Here you will (1) create a simple codebase that makes use of the information given by the document created in the previous scenario, and (2) add a new document that makes reference to this document.


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
5. Returning to the developer perspective: go to the development environment and, and rebuild the process.



