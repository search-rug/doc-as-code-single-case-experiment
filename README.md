# Single case experiment


In a number of studies we have identified a number of issues on the interplay betweem the disciplines involved in large-scale issues. A recurrent issue across several studies was: A, B, ... and M. This includes an exploratory case study on LOFAR/LOFAR2.0.

In an action research initiated in 2021, in the diagnosing phase we identifie more specific issues related to the general ones previousle mentioned.

This project aims define and validate, by means of an action research process, an alternative documentation approach for ICDs that improves (1) the space for misinterpretation of ICDs when used in a cross-disciplinary setting (2) the communication overhead due to incomplete information in it, and (3) the amount of redundant information scattered on all the artifacts (additional documents and software components) derived from the ICDs.


For this first cycle of the actio research, in the action-taking we found that docs-as-code philosply was seemenlgy applicable to many of the identified issues. This is based in three principles: .....
On top of this phyolosphy, the following features are being proposed to address these issues:

- A markdown-based language  over WYSIWYG (word, wikis, etc). Allows to reuse proven tools that allows for collaboration (writing and reviewing) and automation (document building, validation) and ensures consistency and traceability (e.g., version control).

- Quality gates: given the aforementioned automation features, in a docs-as-code documentation pipeline, quality criteria for a document can be defined for a document to be published. This encourages uniformity.

- Documentation as a single source of truth: the machine-readable nature of the  markup-based languages enable writers to embed formal specifications in the documentation (e.g., for hardware) so that from it can be automatically generated  not only the document, but other related artifacts that are prone to error when transcribed from it.

- A documentation-centric dashboard, with an under-the-hood platform that keeps track of the status of the documents, including their dependencies.




## Notes

- The set of tools are fully functional, but on an early development stage.
- The default quality gates:

# Scenario one - Document versioning and review process

In this scenario you will assume the role of a technical writer, who is going to add the final changues to a work-in-progres document, candidate to be published as the first version of an official ICD. When the changes on the git repository are commited and pushed into the remote repostitory in github, it will launch an CI/CD process that will build the document. If no version tag is assigned, the process will publish the generated document in an 'staging environment' for preview. Once the input has been provided (by informal means, or explicitly through pull requests), an official version can be published by adding a version tag.


1. Clone this repositor in your workstation. Install an editor (AsciidoctorFX, ... etc) and edit the index.adoc. file. The file already contains a basic template of the document .
2. Add changes, commit and push them to the repository.
3. Open the [documentation dashboard](http://asdasd.com), authenticate with the credentials send to you by email. Once the documents has been generated, one of two statuses should be available shown: 

NOT PUBLISHED - FAILED REVIEW SUMBISSION
NOT PUBLISHED - NEW STAGING VERSION

If the first case, there was an error. Check the details on the dashboard (PEEK animation of)
In the latter, check and open the URL of the staging version  (for sharing with the involved people).

Now add changes, commit them, and tag the current version. Check in the dashboard that the status is PUBLISHED. Check the link where the offcial version should be created, and that your tag is included as a description.

``bash
    $ git tag -a -m ....
``

# Scenario two - Quality gates

The concept of 'quality gate' refers to the acceptance criteria a project must met before proceding to the following phase. In software, automatically verifiable criteria is integrated in the CI/CD environment, making sure that a version that doesn't fullfill one or more of them doesn't make it to the next stage. In the context of docs-as-code for ICDs management two main criteria were defined: prose quality and clarity, and completeness of technical information. In this proof of concept, two features are 'enforced' as quality gates: undefined acronyms (they must be explicitly linked to a centralized glossary) and, consistency and completeness of SystemRDL models embedded in it.

1. Add a SystemRDL model. The following one, represents a registersmap for a device called 'turboencarbulator'. However, it has an issue, the addresses overlap:


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

2. Add a statement, that uses an acronym. Test the document locally, or commit/add a new version tag. Check the details on the dashboard. Look for details on the failed document version:

- Error - inconsistnecy in the systemrdl document
- Failed Quality gates: optional SystemRDL element, that was set as  mandatory in this context. Acronym unresolved.

3. Fix the inconsistencies by:
    - (1) adding [] to the SystemRDL specification, an (2) 
    - Go to the dashboard, select Glossaries, and choose the acronym. Copy the macro.
    - Add the macro to the document.
4.  Commit, set the tag again and push. Look at the generated elements, namely: a human-readable representation of the registry, headers.


# Scenario three - ICDs as the single source of truth, centralized versions/dependencies tracking.

Working with outdated versions of a document (as some of the documents they are based on are updated), or working with artifacts derived from outdated specifications is a common issue (changes are not announced but discovered). This scenario illustrates how, having the ICDs as a the single source of truth (by making them machine-readable), and keeping track of dependencies between documents, would enable features to assist in the prevention of these outdatedness-related issues. The platform/tools are only for illustrative purposes  (C++, CMake), and the example is a minimalist for the sake of simplicity of the exercise.

Here you will: (1) create a  codebase based that makes use on the information given by the document created in the previous scenario, and (2) add a new document that makes refernence to this document.


1. Clone the dummy C++ application from [this repository](http://asdasd).
2. Open the document previously published (on its deployment site). Go to the section where the register map was defined. Copy the header URL
3. Pull the header inside the project. Add it in the CMake file.
```
wget [url]
``` 
3. In the document, copy the URL of the header's checksum, add it in your CMake file as follows. If you don't have CMake installed, do this in this containerized environment:

```C
cmake_minimum_required(VERSION 3.15)

project(hello_world)

file(DOWNLOAD http://localhost:8000/hashcode ${CMAKE_CURRENT_BINARY_DIR}/checksum)
file(STRINGS ${CMAKE_CURRENT_BINARY_DIR}/checksum updated_checksum)
file(SHA256 ./json.hpp current_bytecode)

if (NOT ${updated_checksum} STREQUAL ${current_bytecode})
        message("Outdated library")
else()
        message("OK")
endif()

message("[${current_bytecode}]")
message("[${updated_checksum}]")

add_executable(app main.cpp)

```

4. Suppose you are creating a new ICD that depends on the previous one. Pull the document from [this repository](http://aaaa), and add the previously created document as a refernece (add a paragraph). To do so, go to the dashboard, find the previously created document and copy the asciidoctor macro. 

![copying-docref-macro.gif](copying-docref-macro.gif)

2.Add a new version tag, push it. Check the status of both documents (both should be PUBLISHED)

3.Now, let's assume a change is made to the original document (the one referenced by the new document, and whose content was used the codebase). Let's modify the SystemRDL specification of the 'turboencarbulator' so that _the register X now is 4 bytes insted of 2xxxxx_.

4.Commit, add a new tag version. Check in the dashboard that this new version is the current one. Check how the status of the document changes (these status changes could be notified by other means to the people involved on its writing process). Now, from a developer perspective: run the CMake file again. 




