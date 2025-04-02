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

When you deploy a contract and click the "Check Exploit" button, our platform executes a validation script to determine if your exploit was successful. This validation happens entirely in your browser through the following process:

1. The platform retrieves the `validationMethod` associated with the exercise
2. It creates a context object containing your deployed contract address, wallet information, and `ethers.js` library
3. It executes the validation code against your deployed contract
4. Based on the results, it determines if you've successfully solved the exercise

## Security Considerations: Community-Submitted Exercises

### Important Warning: Potential JavaScript Injection Risk

Community-submitted exercises include custom validation code that runs in your browser when you click "Check Exploit." **This validation code is arbitrary JavaScript that executes in your browser context.**

### Risk of Stored XSS

The validation method is stored on our server and runs in your browser. For unverified exercises, this presents a **potential Stored XSS (Cross-Site Scripting) risk**, as a malicious exercise creator could include harmful JavaScript code in their validation method.

### Verification Status

All exercises on the platform have one of three statuses:

1. **Verified** Exercises reviewed by our team and considered safe
2. **Pending Verification** Exercises submitted by community members awaiting review
3. **Unverified** Exercises that have not been submitted for verification

## Warning Banner for Unverified Exercises

When viewing an unverified exercise, you'll see a prominent warning banner with the following information:

```
This exercise has not been verified by the platform administrators.

The "Check Exploit" button runs JavaScript code submitted by the exercise creator that could potentially access your wallet information or perform unwanted actions.

Only run "Check Exploit" if you trust the exercise creator or have reviewed the code.
```

## Best Practices for Users

To protect yourself when using the platform:

1. **Prioritize verified exercises** that have been reviewed by our team
2. For unverified exercises:
   - Check the creator's reputation and history
   - Review the validation code before running "Check Exploit" (expanded in Debug Information)
   - Consider deploying to a separate wallet with minimal funds if unsure
   - Never interact with suspicious exercises using your main wallet
3. **Report suspicious exercises** to our team immediately

## Viewing Validation Code

For transparency, all unverified exercises display their validation code in the "View Validation Code" section:

1. Scroll to the bottom of the exercise page
2. Expand the "View Validation Code" section
3. Review the JavaScript code that will execute when you click "Check Exploit"

## Verification Process

Our team reviews community-submitted exercises through the following process:

1. **Code Review**: We analyze both the contract code and validation code
2. **Security Testing**: We test for potential XSS and other security vulnerabilities
3. **Functionality Testing**: We verify that the exercise works as intended

## For Exercise Creators

If you're creating exercises for the platform:

1. Write clear, concise validation code that only performs necessary checks
2. Avoid accessing sensitive user information beyond what's needed for validation
3. Submit your exercise for verification to help build trust
4. Document your validation approach in the exercise description

## Conclusion

Our platform balances educational value with security. While we implement safeguards and encourage exercise verification, users should remain vigilant when interacting with community-submitted content.

The risk of executing arbitrary JavaScript when validating unverified exercises is an accepted trade-off for the educational benefits of our platform, but one that requires user awareness and caution.

Remember: **Only run "Check Exploit" on unverified exercises if you trust the creator or have reviewed the validation code yourself.**
