# Prompt claude code

## Context engineer

Context: I’m Working on X, I Need Y Because Z

```text
I'm working on the registration form of a Next.js 14 app with React Hook Form and Zod.
I need to add validation for the "username" field: it should be between 3 and 20 characters,
only lowercase letters, numbers, and underscores, and it can't start with a number. The rest
of the form already uses Zod so I want it to be consistent. Don't touch the fields that
already have validation.

```

Trick: contexto de sessão iniciar com

```text
Session context: I'm working on a REST API with FastAPI and PostgreSQL. The project uses
SQLAlchemy as the ORM, Alembic for migrations, and pytest for tests. The codebase is about
8,000 lines of Python. I'm going to ask you for several changes in this session.
```

## Explain (Don't write code yet)

Antes de executar qualquer coisa pessa para ele explicar. ex:

```text
Before doing anything, explain how you'd approach migrating authentication from JWT to
sessions with Redis. What files you'd touch, in what order, and what risks you see.
Don't write code yet.
```

When the plan looks right, then you give the green light:

```text
The plan makes sense. Start with step 1. When you're done, stop and show me the result
before continuing.
```

That’s Not Quite Right:

```text
Almost, but there's a problem: you're using Promise.all for the database calls, 
and that can saturate the connection pool if the array is large. I want you to make them sequential
with a for...of loop, not in parallel.
```

This correction message has three components: 

- Acknowledgment of what’s right (“almost”) 
- Precise identification of the problem (“you’re using Promise.all”) 
- Reason for the problem (can saturate the pool) 
- Specific correction (for…of loop, not parallel)

**Don’t mix five corrections into a single message. Go one at a time, especially if the changes are interdependent.**

```text
The first thing I need to fix is the error handling: right now you're swallowing the
database errors and returning 200. I want you to rethrow them and let the error
middleware handle them. We'll look at the types after.
```

## Refactoring

- Step 1: Ask for the plan

```text
I have a processOrder function in TypeScript with excessive nesting that mixes validations
with business logic. Before changing anything, explain how you'd refactor it. Keep in mind
that the project uses Jest for tests and the function has existing tests that must keep
passing.
```

- Step 2: Give the green light with constraints

```text
The approach is correct. Start with the validations: extract each check into a function
with a name that explains what it validates. Use early returns to flatten the nesting.
Don't touch the inventory update logic or the email sending yet.
```

- Step 3: Next part

```text
The validations look good. Now refactor the write operations: I want db.inventory.update
and db.orders.update wrapped in a transaction. Look at how transactions are done elsewhere
in the project before writing it.
```

## Codebase

- Explain code

```text
Read src/auth/middleware.py and explain what it does.
I want to understand the business logic, not just the mechanics.
```

- Explain project

```text
Take a look at the project structure.
What are the main modules? How does data flow
from an HTTP request to the response?
```

A sequence that works well: 
1. High-level architecture overview 
2. Identify the module you need to work on
3. Ask for a detailed explanation of that module 
4. Ask about that module’s dependencies 
5. Work with full context

- Add feature
	1. "Explain how [related module] works"
	2. "What pattern do similar modules follow?"
	3. "Given that pattern, how would you implement [the new feature]?"
	4. Review the proposal
	5. "Implement [the feature] following that pattern"
	6. Review the generated code
	7. "Add tests for this implementation, following the style of existing tests in tests/test_export.py"
	
## Document

1. high-level map

```text
I just inherited this project and I have no documentation.
Can you explore the repository structure and tell me:

1. What this application does
2. What the main modules are and their purpose
3. What technologies it uses and why (as far as you can infer)
4. Which parts look the most complex or critical
```

2. main data flow

```text
Got the general structure. Now walk me through the flow
of a typical [what the app does] operation.
What happens from the moment a request comes in to when the response goes out?
Read whatever files you need to.
```

3. the dark corners

```text
You mentioned that src/pipeline/transformer.py is complex.
Read it and explain it to me in detail. Especially the data
transformation logic and why there's that huge try/except block
on line 87.
```

4. generate documentation

```text
Now that we've explored the project, generate:

1. A general README.md with a high-level overview
2. Docstrings for the functions in src/pipeline/transformer.py
3. Inline comments in the most obscure blocks of transformer.py
```

- Docstrings and Inline Comments

```text
Read src/auth/token_service.py and add docstrings to all
public functions. Follow Google Style format
(Args, Returns, Raises). Don't document the obvious:
focus on non-obvious behavior and edge cases.
```

- Module Docs

```text
Generate a module docstring for src/billing/proration.py
that explains what this module does, when it's used,
and the assumptions about the billing calendar
that the proration logic makes.
```

- README

```text
Generate a README.md for the src/export/ directory
that explains the exporter architecture,
how to add a new format, and external dependency requirements
(third-party libraries that need to be installed).
```

- Updating Dependencies

```text
I want to update Django from 3.2 to 4.2.
Can you review the project and tell me which parts
of the code are most at risk of being affected?
Look for deprecated API usage, patterns that have changed,
and anything you know changed between those versions.
```

For minor can be more straightforward
```text
requests is at 2.28.0 and there's an update to 2.31.0.
Are there any behavior changes between those versions
that could affect how we use it in this project?
```

## Debug

- Temporal context

```text
Production bug. Payment system is down.
Users can't complete checkout.

Error in Sentry (last 10 minutes, 847 occurrences):
Stripe::CardError: Your card was declined. (code: card_declined)
  at src/payments/stripe_client.rb:43:in `charge`
  at src/orders/checkout.rb:156:in `process_payment`
  at src/orders/checkout.rb:89:in `complete`

This error started at 10:47 PM. We haven't deployed anything since 5 PM.
Payments were working fine until then.

Read src/payments/stripe_client.rb and src/orders/checkout.rb
```

- When the initial analysis isn’t enough:

Debugging is a dialogue. You provide information, Claude proposes hypotheses, you verify them, you provide more information. The cycle repeats until you find the root cause.

```text
I've checked what you suggested. The API keys are fine.
I've checked the Stripe dashboard and they're receiving the requests.
The error Stripe is returning is: insufficient_funds (not card_declined).

Why is our code logging card_declined
when Stripe is returning insufficient_funds?
Search the code for where Stripe errors are mapped.
```

- Once you’ve found the bug:

```text
Found it: in stripe_client.rb line 43,
we're mapping all CardErrors to 'card_declined'
without looking at the error's specific code.
The fix is to propagate the actual error code.
Can you make the minimum fix needed for production?
We'll do the complete fix with tests afterward.
```

**The instruction “minimum fix for production” is deliberate. During an active incident, you don’t want a complete refactor of the error system.**

## Test

- Trivial tests

Don’t write an elaborate prompt

```test
Write comprehensive unit tests for the payments/processor.py module.
Use pytest. Include success cases, expected errors, and edge cases.
```

**check that the code does what it says it does on the happy path.**

- Useful tests

be explicit:

```test
Write tests for payments/processor.py. I want:

- Cases where the card is expired
- Cases where the payment gateway returns a timeout
- Cases where the amount is negative or zero
- Cases where the user has insufficient funds
- What happens if the Payment object comes in with None fields
Don't give me tests for "a valid payment works" — I already know that.
```

- edge cases, errors, and real-world scenarios

```text
The pricing.py module calculates prices for a SaaS with monthly and annual plans.
Customers can have accumulated discounts. The price can never be negative.
There's a free plan with a $0 limit.

Write tests that cover:

- Discount accumulation (can it exceed 100%?)
- The exact behavior when the result would be negative
- Transitioning between plans when there's an active discount
- Annual renewal with a monthly discount applied
```

```text
Read the code in auth/tokens.py. Now act like someone trying to find
a security bug or make the system behave incorrectly. What inputs
would you try? Write tests for those cases.
```

### TDD

Step 1: Write the tests yourself, or describe the behavior you want. 

Step 2: Claude implements the code to pass those tests. 

Step 3: The tests tell you whether the implementation is correct.

```text
I want to implement a rate limiter for my API. Before implementing it,
write the tests it should pass:

- Maximum 100 requests per minute per IP
- The counter resets every 60 seconds exactly
- Whitelisted IPs have no limit
- When the limit is exceeded, return 429 with the correct Retry-After header
- The limit is per IP, not global

Write only the tests, with all the necessary mocks. I'll do the implementation later.
```

After tests generated

```text
Now implement RateLimiter in src/middleware/rate_limiter.py so that
all the tests you just wrote pass. Use Redis for storage.
```

### E2E

- For integration tests

```text
Write integration tests for the user registration flow:

1. The POST /api/users endpoint receives email and password
2. Validates the email format (using EmailValidator)
3. Hashes the password with bcrypt
4. Saves to PostgreSQL (users table)
5. Sends a welcome email via SendGrid
6. Returns 201 with the user_id

Mock SendGrid and use a real test database (I already have fixtures).
Cover: successful registration, duplicate email, invalid email format,
password too short, database failure.
```

- Playwright, Cypress, Selenium, and most testing frameworks:

```text
Write E2E tests with Playwright for my e-commerce checkout flow.
The user: searches for a product, adds it to the cart, enters card details
(use Stripe's test data: 4242 4242 4242 4242), completes the purchase,
sees the confirmation page with an order number.

The base URL is localhost:3000. Use page object model.
Write the tests in TypeScript.
```

### Refactory Tests

- Change functionality

```text
I just refactored OrderProcessor. The process_order() method now
accepts an Order object instead of separate parameters, and returns
an OrderResult instead of a dict.

Here are the current tests: [paste the tests]
Here's the new code: [paste the refactored code]

Update the tests so they work with the new interface,
keeping the same case coverage.
```

- New functionality

```text
I added discount support to PaymentProcessor. Now process_payment()
accepts an optional discount_code parameter. Here's the new code.

The existing tests pass. Add tests for the new cases:
valid discount, expired discount, discount for a different product,
discount that brings the price to 0, discount that doesn't exist.
Don't modify the existing tests.
```

### No test code

1. Give Claude Code the context.

```text
I'm going to give you three files from a payments module. I need a
complete test suite. Read all the files before generating anything.
```

2. Ask for the plan first.

```text
Before writing the tests, tell me:

1. What main functionalities you've identified
2. What edge cases you think are critical in a payments module
3. How you'd structure the test files
```

3. Generate the tests in order of criticality.

```text
Generate the tests for validators.py first. I want special coverage
for card number validation (Luhn algorithm), expiration dates,
and CVV. Use pytest with parametrize for the validation cases.
```

Then:

```text
Now the tests for processor.py. Mock the gateway completely.
Especially cover: transactions that Stripe rejects, timeouts,
retries, and the state of the Payment object after each scenario.
```

4. Adjustments and execution.

```text
This test fails with:
AttributeError: 'PaymentResult' object has no attribute 'transaction_id'

The attribute is called 'stripe_charge_id'. Fix the affected tests.
```

5. Add new tests

```text
Add a test for duplicate subscription: the same user_id already has
an active subscription for the same plan_id. It should return 409.
```

6. Implement

```text
Now implement the POST /api/subscriptions endpoint in
api/routes/subscriptions.py and the business logic in
services/subscription_service.py.

All the tests we just wrote should pass.
Use FastAPI, async SQLAlchemy, and the official stripe library.
```