# Proposals
By default, standard contributors cannot commit directly to a locked branch, instead they must submit a proposal branch to be considered and voted on.

## Permission to Propose
In order to make a proposal, a user must have at least the reputation points given by `git config --user.<name>.scoreToPropose` (See User Points).

## Proposal Structure

### Code
A **PROPOSAL** is a branch of a LOCKED BRANCH containing one or more consecutive commits, referenced with `refs/heads/proposals/<id>`, where `<id>` is the commit hash at the head of the PROPOSAL.

A PROPOSAL is said to be "**OF** branch `<x>`" if it is based on either:

* A commit in the branch `<x>`
* A commit in another PROPOSAL, which is itself of branch `<x>`
	* *Note: It does not need to be the head commit*

A proposal must be OF a LOCKED BRANCH.

**DIAGRAM**

### Meta Data
The meta data for a PROPOSAL shall be stored in the tracking branch, under: `.tracking/proposals/<id>/`, where `<id>` is the commit hash at the head of the PROPOSAL. This directory is called the **PROPOSAL DIRECTORY**.

#### File names
There should always be one file within the PROPOSAL DIRECTORY named "proposal". Other file names are used for REVIEWS and are named after the reviewer:

<pre>
filename := "proposal" / review
review   := &lt;Addr-Spec as defined by <a href="http://www.ietf.org/rfc/rfc2822.txt">RFC2822</a>&gt;
</pre>

#### File Format
Files within the PROPOSAL DIRECTORY shall contain any number of name value pairs, followed by text:

<pre>
file   := *(header) "\r\n" text
header := name ":" value "\r\n"
name   := &lt;Any letter, number or hyphen&gt;
value  := &lt;Any (US-ASCII) CHAR, leading and trailing whitespace is ignored&gt;
text   := &lt;Any (US-ASCII) CHAR)&gt;
</pre>

#### Proposal File
When creating a PROPOSAL, the PROPOSER should create a file named "proposal" in the PROPOSAL DIRECTORY.

##### Headers
<table width="100%">
	<tr>
		<th>Header</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>Proposer</td>
		<td>The name and email address of the proposer, as specified by <a href="http://www.ietf.org/rfc/rfc2822.txt">RFC2822</a> <i>(required)</i></td>
	</tr>
	<tr>
		<td>Submitted-at</td>
		<td>The <a href="http://www.ietf.org/rfc/rfc2822.txt">RFC2822</a> timestamp at the time of submmiting the proposal <i>(required)</i></td>
	</tr>
	<tr>
		<td>Extension-of</td>
		<td>Specifies the id of the PROPOSAL this extends <i>(required when this proposal is an extension)</i></td>
	</tr>
	<tr>
		<td>Fix-of</td>
		<td>Specifies the id of the PROPOSAL this fixes <i>(required when this proposal is a fix)</i></td>
	</tr>
	<tr>
		<td>Alternative</td>
		<td>This header may appear multiple times. It specifies the ID of a PROPOSAL which fixes this proposal <i>(required when fix proposals for this proposal exist)</i></td>
	</tr>
	<tr>
		<td>Merged-By</td>
		<td>This header may appear multiple times. It specifies the ID of a PROPOSAL which merges this proposal into the LOCKED BRANCH <i>(required when merge proposals for this proposal exist)</i></td>
	</tr>
	<tr>
		<td>Merges</td>
		<td>Specifies the ID of a PROPOSAL which this PROPOSAL merges into the LOCKED BRANCH <i>(required when this proposal is a merge proposal)</i></td>
	</tr>
		
</table>


## Review of Proposal


### Review Process
* A peer must have the required number of points before they are able to review a proposal (See Section 4 – User Credibility)
* A peer who wishes to review a commit should create a file within the commit directory.
* Peers may not edit another peer’s review
* A peer may change their own review at any time by appending their new review to the file. They must not delete the old review.


### Review Vote
* Reviews may contain an accept or reject vote
* In order for a proposal to be accepted, it must reach the threshold defined in the configuration, this can be either:
  * Percentage based
  * Absolute net positive vote count
* The Proposer Weight sets the required threshold for positive votes
* Votes are weighted based on the Reviewers’ Weights


## Accepting a Proposal
* Any peer may merge an accepted proposal into the locked branch when it reaches the required threshold.


## Weeding Rejected Proposals
* A proposal that fails to secure the required weight after a certain amount of time, or which receives a threshold number of negative votes may be cleared from the system.
* The Proposal’s Review Directory must be left unremoved


## Creating Supplementary Proposals
Once a proposal has been submitted it may only be approved or rejected, it cannot be amended. However new proposals may be made, based on it:


### Extensions
Sometimes it may be desirable to make a proposal based upon another proposal, to be accepted when its parent is approved. When this occurs, an accepted extension proposal is only merged into the locked branch once its parent is approved.

 


### Fixes and Alternatives
A fix proposal is intended to simply correct a proposal, as such it should:
* Only include bugfixes
* Endeavour not to change function names / API endpoints that may be used by other code
* Not add new features
When users review a proposal, they will be informed of alternative proposals, and encouraged to choose either/or.

When an approved fix proposal is based upon a proposal, it shall be merged even if the proposal on which it is based has not been: