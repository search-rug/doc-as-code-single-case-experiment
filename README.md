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





- missing key elements in hardware specifications.
- undefined acronyms
- 

given the its 'computability', some of these criteria ca
In the context of an automated CI/CD pipeline, some of these quality gates can be automatically verified,. In the case of documentation,  

A quality-gate is an acceptance criteria




NOT PUBLISHED - FAILED REVIEW SUMBISSION
NOT PUBLISHED - NEW STAGING VERSION
PUBLISHED
PUBLISHED - FAILED REVIEW SUBMISSION
PUBLISHED - FAILED VERSION SUBMISSION
PUBLISHED - DEPENDENCY UPDATED
