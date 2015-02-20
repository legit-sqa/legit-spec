# Reviews

## Permission to Review

In order to make a REVIEW, a user must have at least the reputation points given by `git config --user.<name>.scoreToReview` (See `User Points`).

## Review Structure
REVIEWS are saved in the PROPOSAL DIRECTORY in a file named after the REVIEWER (See `Proposal Structure` &gt; `Meta Data` &gt; `File Names`).

### Headers
<table>
	<tr>
		<th width="100px">Header</th>
		<th>Description</th>
	</tr>
	<tr>
		<td>Reviewer</td>
		<td>The name and email address of the REVIEWER, as specified by <a href="http://www.ietf.org/rfc/rfc2822.txt">RFC2822</a> <i>(required)</i></td>
	</tr>
	<tr>
		<td>Reviewed-at</td>
		<td>The <a href="http://www.ietf.org/rfc/rfc2822.txt">RFC2822</a> timestamp at the time of submmiting the REVIEW <i>(required)</i></td>
	</tr>
	<tr>
		<td>Decision</td>
		<td>Either "Accept" or "Reject"</td>
	</tr>
</table>

## Submitting a Review

When reviewing a PROPOSAL, a peer should:

* Create a file in the PROPOSAL DIRECTORY. The filename should be their email address (See `Proposals` &gt; `Structure` &gt; `Meta Data` &gt; `File Names`) and fill it with their review information:

<pre>
Review: John Doe &lt;john.doe@example.net&gt;
Submitted-at: Mon, 22 Feb 2015, 16:59:00 +0000
[Decision: Accept]

Text describing the review...
</pre>

* Update their USER FILE, incrementing the value of the `Reviews` Header by one.
* Update the "proposal" file, with an incremented value for the `Votes` Header, capping the votes at the threshold (See `Calculating Weighted Votes` and `Calculating Required Vote`):

<pre>
vote_header := min(incremented_vote, required_vote)  if incremented_vote > 0
            := max(incremented_vote, -required_vote) otherwise

incremented_vote := old_vote_header_value + weighted_vote
</pre>

These changes should be in a single commit on the `.tracking` branch, with the message:

`Reviewed Proposal: <id>`

### Calculating Weighted Votes

A peer's **WEIGHTED VOTE** is given by the following equation:

<pre>
weighted_vote  := vote * min(scaled_vote, config.score.maxVote)
scaled_vote    := floor(1 + score * config.score.reviewWeight)

vote           :=  1 if decision = "Accept"
               := -1 if decision = "Reject"
               :=  0 otherwise
</pre>

## Calculating Required Vote

A proposal's required vote is given by:

<pre>
required_vote := max(weighted_vote, config.score.minVotes)
weighted_vote := ceil(config.score.requiredVotes - score * config.score.proposalWeight)
</pre>

## Accepting or Rejecting a Proposal
If, after reviewing a proposal, the proposal has reached either the positive or negative number of required votes (See `Calculating Required Vote`), the peer SHOULD close the PROPOSAL:

### Rejecting a Proposal
A peer rejecting a proposal MUST make the following changes to the `tracking` branch in a single commit, with the message `Rejected Proposal: <id>`:

* For each reviewer, update their USER FILE:
	* Increasing the number of `Bad-Reviews` if the reviewer voted to accept the PROPOSAL
	* Increasing the number of `Good-Reviews` if the reviewer voted to reject the PROPOSAL
* Update the original PROPOSER'S USER FILE, increasing the number of `Bad-Proposals` by one.
* Update the "proposal" file, changing the status to "Rejected"


### Accepting a Proposal

#### Marking as Accepted
A peer marking a proposal as ACCEPTED MUST make the following changes to the `tracking` branch in a single commit, with the message `Accepted Proposal: <id>`:

* For each reviewer, update their USER FILE:
	* Increasing the number of `Good-Reviews` if the reviewer voted to accept the PROPOSAL
	* Increasing the number of `Bad-Reviews` if the reviewer voted to reject the PROPOSAL
* Update the original PROPOSER'S USER FILE, increasing the number of `Good-Proposals` by one.
* Update the "proposal" file, changing the status to "Accepted"

#### Merging
A peer ACCEPTING a PROPOSAL MUST check to see if the PROPOSAL can be merged into the LOCKED BRANCH. An **APPROVED PROPOSAL** may be merged if:

* It is based directly on the LOCKED BRANCH, or
* It is based on a PROPOSAL which has been merged, or
* It is based directly on a PROPOSAL for which this is a FIX, and that PROPOSAL meets one of these three conditions.

If the PROPOSAL matches one of the above conditions, the peer MUST attempt to merge the PROPOSAL into the LOCKED BRANCH.

##### Merging without Conflict
If the PROPOSAL can be merged into the LOCKED BRANCH without causing a merge conflict, the peer MUST merge it into the LOCKED BRANCH in a single commit with the message `Merged Proposal <id>`. This commit may *only* be a merge - no new lines of code may be added.

##### In the case of a Merge Conflict
If merging the PROPOSAL into the LOCKED BRANCH causes a merge conflict, the peer MAY attempt to create a MERGE PROPOSAL.

When submitting the MERGE PROPOSAL to the `.tracking` branch, the peer MUST add the `Merged-By` header to the PROPOSAL it is based on.

## In this Section

`min`, `max`, `floor` and `ceil` functions are used in the examples in this section. They are defined as follows:
<pre>
min(x, y) := x if x &lt;= y
          := y otherwise

max(x, y) := x if x &gt;= y
          := y otherwise

floor(x)  := x - (x % 1)

ceil(x)   := x               if x % 1 = 0
          := x + (1 - x % 1) otherwise
</pre>