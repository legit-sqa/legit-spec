
# Terms

## Locked Branch
A branch which cannot be directly committed to without peer review

## Proposal
A branch of a locked branch with proposed new code

## Proposer
A user who creates and publishes a proposal 

## Proposer Weight
The weight of votes required by a proposal is related to the proposer’s points

## Reviewer
A user who reviews a proposal

## Reviewer Weight
The weight of a vote cast by a reviewer is related to their points

## Review
A comment on a proposal by a review, which may contain an accept or reject vote

## Vote
Reviews may contain an accept or reject vote; when a proposal reaches the required number of weighted accept votes, it is accepted and merged into the locked branch. This takes into account the Proposer Weight and all relevant Reviewer Weights on the accept reject sides, and determines if a proposal has been accepted according to the vote threshold specified in the configuration.

# Proposals
* A user must have the required number of points before they are able to submit a proposal (See Section 4 – User Credibility).
* Each Commit should have its own directory within the tracking directory, specified by the commit hash.

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

 

# User Credibility (Points)
* Having a proposal accepted
  * Weighted on threshold
* Opening rankings

## Log
* As each operation takes a while to verify, a log will be kept of verified values, to avoid the need to reverify the entire tree when a user tries to perform a restricted action
* Keep a record of:
  * Number of proposals
    * Accepted
    * Rejected
    * Vote count
  * Number of reviews
    * Accepted
* Good Accept
* Bad Accept
    * Rejected
* Good Reject
* Bad Reject


# Accepting a Push or Pull
Check that all commits are acceptable, commits may be:
* Commits made by someone with direct access
* Merge commits
* Proposal merges, assuming the proposal has correct amount of votes


## Merge Resolution
* Create a new proposal with merged code
* Refer to admin
* When deciding a winner
  * Highest weighting
  * Earliest acceptance
  * Greatest number dependant commits
  * 
