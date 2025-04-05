---
weight: 50
title: "Exercise Validation and Security"
description: "How exercises are validated and the security implications"
icon: "security"
date: "2025-04-02T08:00:00+02:00"
lastmod: "2025-04-02T08:00:00+02:00"
draft: false
toc: true
---

This documentation explains how exercise validation works on our Smart Contract Auditing platform and the security considerations you should be aware of when working with community-submitted exercises.

## How Exercise Validation Works

When you deploy a contract and click the "Check Exploit" button, our platform executes a validation script to determine if your exploit was successful. The entire check is performed server-side.

## Exercise Status Levels

All exercises on the platform have one of three status levels:

1. **Private** - Newly created or modified exercises that are only visible to their creator as well as users with the link.
2. **Verified** - Exercises reviewed and approved by our administration team.
3. **Public** - Verified exercises that have been made available to all users.

## Security Considerations

### Verification Process

Our team employs a rigorous verification process:

1. **Submission** - Exercise creators can request verification through the platform
2. **Review** - Our administrators review both the vulnerable contract and validation code
3. **Approval/Rejection** - Exercises are either approved (status changed to "verified") or rejected
4. **Publication** - Verified exercises may be further promoted to "public" status for broader visibility

## Verification Requests

### For Exercise Creators

When you create or modify an exercise:

1. Your exercise starts with a "private" status
2. You can request verification by:
   - Editing your exercise
   - Going to the "Publication" tab
   - Checking "Request verification" or "Request publication"
   - Adding a note for administrators (optional but recommended)
   - Submitting your changes

## Warning Banner for Unverified Exercises

When working with unverified exercises, you'll see a prominent warning banner:

```
This exercise has not been verified by the platform administrators.
```

## For Exercise Creators

When creating exercises for the platform:

1. Write clear, concise validation code that only performs necessary checks
2. Provide detailed explanations in your verification request notes
3. Be prepared to make adjustments based on administrator feedback
4. Document your validation approach in the exercise description
