---
aip: 1
title: Almight Improvement Prposals Purpose and Guidelines
status: Living
type: Meta
author: Piyush Jaiswal <iampiyushjaiswal103@gmail.com>
created: 2022-08-22
updated: 2022-08-22
---
# Almight Improvement Proposals
Amight Improvement Proposals (AIPs) describe specs and standards for the Almight platform, including the core backend, external backend services, client APIs, and contract standards.

## AIP Rationale
We intend AIP to be the primary mechanisms for proposing new features, for collecting community technical input on an issue, and for documenting the design decisions that have gone into Almight. 

## AIP Types
There are three types of AIP:
* An <strong>Informational AIP</strong> describes Almight design issue, or provides general guidelines or information to the Almight community, but does not propose a new feature. Informational AIPs do not necessarily represent Almight community consensus or a recommendation, so users and implementers are free to ignore Informational AIPs.

* A <strong>Meta AIP</strong> deescribes a process surrounding Almight or proposes a change to (or an envent in) a process. Process or Meta AIPs are like Standard AIPs but apply to areas toher than Almight Implementations itself. They may propose an implementation, but not to Almight codebase; they often require community consensus; unlike Informational AIPs, they are more than recommendations, and users are typically not free to ignore them. Examples include procedure, guidelines, external standards, changes to the decision-making process, and changes to the tools or environment used in the Almight development.

* A <strong>Standard AIP</strong> describes any changes that affect most all ALmight implementations, such as -- a change to the authentication procedure, addition of methods supported in Protocol, etc. Standard AIPs consist of three parts -- a design document, an implementation, and (if warranted) and update to the formal specification.
Standard AIPs will have category associated with them indicating their relation with the module they'll be used in. Examples include an AIP related authentication of users will reside under the authentication module. authentication, storage service, client libs, protocol are the services which are called modules in AIP.

## AIP Status

**Idea** - An idea that is pre-draft. Can be directly discussed in `issues`
**Draft** - The first formally tracked stage of an AIP development.
**Review** - An AIP author marks an AIP as ready for and requesting Peer Review.
**Final** - This AIP represents the final standard. A Final AIP exists in a state of finality and should only be updated to correct errata and add non-normative clarifications.
**Stagnant** - Any AIP in `Draft` or `Review` if inactive for period of 1 months or greater is moved to `Stagnant`. An AIP may be resurrected from this state by authors or AIP Editors through moving it back to `Draft` or it's earlier status.
**Withdrawn** - The AIP Author(s) have withdrawn the proposed AIP. This state has finality and can no longer be resurrected using this AIP number. If the idea is pursued at later date it is considered a new proposal. 
**Living** - A special status of AIPs that are designed to be continually updated and not reach a state of finality. This includes most notably AIP-1.

## What belongs in a successfull AIP?
Each AIP should have the following parts:
* Preamble - RFC 822 style headers containing metadata about the AIP, including the AIP number, a short descriptive title, a descriptions, and author details. 
* Abstract - Abstract is a multi-sentence (short) technical summary, This should be a very terse and human-readable version of the specification section.
* Motivation (optional) - A motivation sectional is critical for AIPs that want to change the current implementation of Almight. It should clearly explaing why the existing implementation is inadquate to address the problem that the AIP solves.
* Specification - The technical specification should describe the syntax and semantic of any new feature. The specifiction should be detailed enough to allow competing, interoperable implementations for any of the current technologies.
* Rationale - The rationale fleshes out the specification describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related worl, e.g how the feature is supported in other languages. The rationale shoudl discuss important objections or concers raised during discussion around the AIP.
* Backwards Compatibility (optional) - All AIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their consequences. The AIP must explain how the author proposes to deal with these incompatibilities. This section may be omitted if the proposal does not introduce any backwards incompatibilities, but this section must be included if backward incompatibilities exist.
* Test Cases (optional) - Test cases for an implementation are mandatory for AIPs that are affecting consensus changes. Tests should either be inlined in the AIP as data (such as input/expected output pairs, or included in `../assets/aip-###/< filename >`. 
* Reference Implementation (optional) - An optional section that contains a reference/example implementation that people can use to assist in understanding or implementing this specification. This section may be omitted for all AIPs.
* Security Considerations - All AIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life-cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. AIP submissions missing the “Security Considerations” section will be rejected. An AIP cannot proceed to status “Final” without a Security Considerations discussion deemed sufficient by the reviewers


## AIP Header Preamble
Each AIP must begin with an RFC 822 style header preamble, preceded and followed by three hyphens (`---`). This header is also termed [“front matter” by Jekyll](https://jekyllrb.com/docs/front-matter/). The headers must appear in the following order.

* `aip`: AIP identifier; 

* `title`: The AIP title is a few words, not a complete sentence

* `description`: Description is one full (short) sentence

* `author`: The list of the author’s or authors’ name(s) and/or username(s), or name(s) and email(s).

* `discussions-to`: The url pointing to the official discussion thread

* `status`: Draft, Review, Last Call, Final, Stagnant, Withdrawn, Living

* `type`: One of Standard , Meta, or Informational

* `module`: The module / service name for which the AIP is purposed. For e.g `asta/authentication` for authentication module in asta, `storage_server` for storage server.

* `created`: Date the AIP was created on

* `requires`: AIP number(s) (Optional field)

* `withdrawal-reason`: A sentence explaining why the AIP was withdrawn. (Optional field, only needed when status is Withdrawn)

Headers that permit lists must separate elements with commas.

Headers requiring dates will always do so in the format of ISO 8601 (yyyy-mm-dd).

`aip` header
Unique identifier must be formatted according to the name purpose.
Standard AIP format :`aip-xxx` or `aip-{module}-xxx`. Examples include `aip-authentication-102` or `aip-authentication-103` or `aip-storage-002`
Mata AIP format: `aip-meta-xxx` or `aip-{module}-meta-xxx`
Informational AIP format: `aip-info-xxx` or `aip-{module}-info-xxx`

`author` header
The author header lists the names, email addresses or usernames of the authors/owners of the AIP. Those who prefer anonymity may use a username only, or a first name and a username. The format of the author header value must be:

Random J. User <address@dom.ain>

or

Random J. User (@username)

if the email address or GitHub username is included, and

Random J. User

if the email address is not given.

It is not possible to use both an email and a GitHub username at the same time. If important to include both, one could include their name twice, once with the GitHub username, and once with the email.

At least one author must use a GitHub username, in order to get notified on change requests and have the capability to approve or reject them.

## Linking to other AIPs
References to other AIPs should follow the format AIP-N where N is the AIP number you are referring to. Each AIP that is referenced in an AIP MUST be accompanied by a relative markdown link the first time it is referenced, and MAY be accompanied by a link on subsequent references. The link MUST always be done via relative paths so that the links work in this GitHub repository, forks of this repository, the main AIPs site, mirrors of the main AIP site, etc. For example, you would link to this AIP with [AIP-1](/AIPS/aip-1).

## History
This document is copy-paste from [Ethereum's EIP-1](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1.md) with required changes.