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

#### Proposal File
When creating a PROPOSAL, the PROPOSER should create a file named "proposal" in the PROPOSAL DIRECTORY.

##### Headers
<table width="100%">
	<tr>
		<th width="100px">Header</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>Proposer</td>
		<td>The name and email address of the proposer, as specified by <a href="http://www.ietf.org/rfc/rfc2822.txt">RFC2822</a> <i>(required)</i></td>
	</tr>
	<tr>
		<td>Votes</td>
		<td>The numerical score of the number of WEIGHTED VOTES <i>(required)</i></td>
	</tr>
	<tr>
		<td>Status</td>
		<td>
			The status of the review. One of:
			<ul>
				<li>Open</li>
				<li>Accepted</li>
				<li>Rejected</li>
			</ul>
		 	<i>(required)</i>
		</td>
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
		<td>Extended-by</td>
		<td>This header may appear multiple times. It specifies the ID of a PROPOSAL which extends this proposal <i>(required when extension proposals for this proposal exist)</i></td>
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

## Submitting a Proposal

When submitting a PROPOSAL, a peer should:

* Create the PROPOSAL DIRECTORY and fill the "proposal" file with their details, and the details of the proposal:

<pre>
Proposer: Joe Bloggs &lt;joe.blogs@example.net&gt;
Submitted-at: Mon, 22 Feb 2015, 16:59:00 +0000
Votes: 0
Status: Open
[Extension-of: ID]
[Merge-of: ID]
[Fix-of: ID]

Text describing the proposal...
</pre>

* Update their USER FILE, incrementing the value of the `Proposals` Header by one.

Both these changes should be in a single commit on the `.tracking` branch, with the message:

`Submitted Proposal: <id>`

## Types of Proposals
All new PROPOSALS are based directly on the LOCKED BRANCH, however when they are based instead on another PROPOSAL, they must be one of the following types:

### Extensions
An **EXTENSION** is a PROPOSAL which is based on another PROPOSAL, and relies on features that it introduces. When an EXTENSION is APPROVED, it is *not* merged until all PROPOSALS below it are APPROVED.

As such, when a PROPOSAL is APPROVED, the client should also check if there are any APPROVED EXTENSIONS and MERGE them at the same time. 

### Fixes and Alternatives
A **FIX** is a PROPOSAL which modifies features that another PROPOSAL introduces. A FIX does not need to be based on the PROPOSAL which it fixes.

#### Approving a Fix

When a FIX is APPROVED, any other FIXES for the same PROPOSAL are automatically deemed to be REJECTED.

##### The FIX is based on the PROPOSAL it fixes
A PROPOSAL on which a FIX is based does *not* need to be APPROVED in order for a APPROVED FIX to be MERGED.

##### The FIX is not based on the PROPOSAL it fixes
A PROPOSAL on which a FIX is not based is automatically deemed to be REJECTED when the FIX is APPROVED.

### Merges
When MERGING an APPROVED PROPOSAL results in a merge conflict, any user may attempt to solve the merge, and submit it as a new **MERGE PROPOSAL**.

A MERGE PROPOSAL must be a single commit, with the parents:

* The head of the original PROPOSAL
* The head of the LOCKED BRANCH OF the original PROPOSAL

When a MERGE PROPOSAL is APPROVED, any other MERGE PROPOSALS for the same PROPOSAL are automatically deemed to be REJECTED.
