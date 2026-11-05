# Lab 02: Black-Box Website Testing

## 1. Website and Features

- **Website:** SauceDemo (Swag Labs)
- **URL:** https://www.saucedemo.com/
- **Features tested:** (1) Login and (2) Adding/removing one product in the shopping cart
- **Method:** Black-box test design. I use the website's visible behavior and do not examine its source code.
- **Test account for examples:** Username `standard_user`, password `secret_sauce`. The demo website publishes these sample credentials.

**Testing note:** The tables below are designed *test cases* with expected results, not claims that I executed the tests. When running them, record actual results separately.

## 2. Equivalence Partitioning — Login

Equivalence partitioning means grouping inputs expected to act the same way. I pick one example from each valid or invalid group.

| ID | Partition | Valid/Invalid | Example username | Example password | Expected result |
|---|---|---|---|---|---|
| EP-01 | Active demo user with correct password | Valid | `standard_user` | `secret_sauce` | Login works; Products page opens. |
| EP-02 | Username is missing | Invalid | *(empty)* | `secret_sauce` | Login blocked; username-required error shown. |
| EP-03 | Password is missing | Invalid | `standard_user` | *(empty)* | Login blocked; password-required error shown. |
| EP-04 | Username is not recognized | Invalid | `not_a_real_user` | `secret_sauce` | Login blocked; credentials error shown. |
| EP-05 | Password is wrong | Invalid | `standard_user` | `wrong_password` | Login blocked; credentials error shown. |
| EP-06 | Recognized but locked user | Invalid for login | `locked_out_user` | `secret_sauce` | Login blocked; locked-out error shown. |
| EP-07 | Both fields are missing | Invalid | *(empty)* | *(empty)* | Login blocked; username-required error shown first. |

**How to run EP tests:** Open the login page in a fresh session, enter each row's username and password, select **Login**, and compare the observed page or error with the expected result.

## 3. Decision Table Testing — Login

**Conditions:** Is a username entered? Is a password entered? Do the credentials match a demo account? Is the account locked?

**Actions:** Show a required-field error, show a credentials error, show a locked-account error, or open the Products page.

`—` means the condition is not relevant for that rule.

| Conditions and expected actions | R1 | R2 | R3 | R4 | R5 |
|---|---|---|---|---|---|
| Username entered? | No | Yes | Yes | Yes | Yes |
| Password entered? | — | No | Yes | Yes | Yes |
| Credentials correct? | — | — | No | Yes | Yes |
| Account locked? | — | — | — | Yes | No |
| **Show username-required error** | Yes | No | No | No | No |
| **Show password-required error** | No | Yes | No | No | No |
| **Show credentials error** | No | No | Yes | No | No |
| **Show locked-account error** | No | No | No | Yes | No |
| **Open Products page** | No | No | No | No | Yes |

### Test Cases From the Decision Table

Each test begins on https://www.saucedemo.com/. Fill in the fields indicated, then press **Login**.

| Test case | Rule | Username | Password | Expected result |
|---|---|---|---|---|
| DT-01 | R1 | *(empty)* | `secret_sauce` | Username-required error. |
| DT-02 | R2 | `standard_user` | *(empty)* | Password-required error. |
| DT-03 | R3 | `standard_user` | `wrong_password` | Credentials error; do not log in. |
| DT-04 | R4 | `locked_out_user` | `secret_sauce` | Locked-out error; do not log in. |
| DT-05 | R5 | `standard_user` | `secret_sauce` | Products page opens. |

## 4. State Transition Testing — Shopping Cart

For this test, first sign in as `standard_user`. Focus on **one product**, Sauce Labs Backpack, so the cart is either empty or contains that one product.

### States

- **S0 — Empty cart:** The backpack is not in the cart; no backpack cart badge count is displayed.
- **S1 — Backpack in cart:** The backpack is in the cart; the cart badge indicates `1`.

### Events

- **E1 — Add:** Click **Add to cart** for Sauce Labs Backpack.
- **E2 — Remove:** Click **Remove** for Sauce Labs Backpack.
- **E3 — Try to remove when empty:** Look for a **Remove** control when the backpack is already absent. This action should not be available.
- **E4 — Try to add the same item twice:** After adding the backpack, look for another **Add to cart** control for that same product. The control should have switched to **Remove**.

### State Transition Table

| ID | Starting state | Event | Valid or invalid? | Expected ending state and behavior |
|---|---|---|---|---|
| ST-01 | S0 — Empty | E1 — Add backpack | Valid | S1; cart badge shows `1`, and the product is in the cart. |
| ST-02 | S1 — Backpack present | E2 — Remove backpack | Valid | S0; backpack is absent and the cart badge does not show `1`. |
| ST-03 | S0 — Empty | E3 — Try to remove backpack | Invalid/not available | S0; no Remove button for that product is available; cart stays empty. |
| ST-04 | S1 — Backpack present | E4 — Try to add same backpack again | Invalid/not available | S1; the button is **Remove**, so adding a duplicate from that button is not possible; cart remains at `1`. |

### Test Cases From State Transitions

| Test case | Steps | Expected result |
|---|---|---|
| ST-TC-01 | Log in; start with an empty cart; select **Add to cart** for Sauce Labs Backpack; open the cart. | Backpack appears once; cart badge shows `1`. |
| ST-TC-02 | From a cart containing the backpack, select **Remove**; open the cart. | Backpack disappears; cart is empty. |
| ST-TC-03 | With an empty cart, inspect the backpack product button. | **Add to cart** is offered; **Remove** is not available. |
| ST-TC-04 | Add the backpack once; inspect that product's button and cart badge. | Product button changes to **Remove** and the badge stays `1`; no second Add button for that item is offered. |

## 5. Summary

I designed black-box tests for two visible website features. Equivalence partitioning checks groups of valid and invalid login inputs. The decision table connects different login conditions to the expected messages or successful login. State transition testing checks how adding and removing one product changes the shopping cart. I did not need to view the website's source code.

## References

- Website under test: https://www.saucedemo.com/
