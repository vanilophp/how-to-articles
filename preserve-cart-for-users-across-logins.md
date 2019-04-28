---
title: Preserve The Cart For Users Across Logins
excerpt: It is possible to keep the cart for users after logout and restore it after successful login.
date: 2019-04-28
tags: [cart]
---

The cart (by default) automatically handles cart+user associations in the following cases:

| Event                     | State             | Action                    |
|:--------------------------|:------------------|:--------------------------|
| User login/authentication | Cart exists       | Associate cart with user  |
| User logout & lockout     | Cart exists       | Dissociate cart from user |
| New cart gets created     | User is logged in | Associate cart with user  |

To prevent this behavior, set the `vanilo.cart.auto_assign_user` config value to false:

```php
// config/vanilo.php
return [
    'cart' => [
        'auto_assign_user' => false
    ]
];
```

## Preserve The Cart For Users Across Logins

It is possible to keep the cart for users after logout and restore it after successful login.

> This feature is disabled by default. To achive this behavior, set the
> `vanilo.cart.preserve_for_user` config value to true

| Event                     | State                                                             | Action                               |
|:--------------------------|:------------------------------------------------------------------|:-------------------------------------|
| User login/authentication | Cart for this session doesn't exist, user has a saved active cart | Restore the cart                     |
| User login/authentication | Cart for this session exists                                      | The current cart will be kept        |
| User logout & lockout     | Cart for this session exists                                      | Cart will be kept for the user in db |
