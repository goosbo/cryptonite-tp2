# La Casa de Papel

After analysing the code given, I figured that name must contain `Bob` in the `Let's Fool Alice!` option for it to let u pass to the next stage.

```
Bot: Okay, let's see if you're the real deal. What's your name?
Your name: Bob

Bot: Please provide your HMAC
```

The HMAC seemed to be a function of the username entered.
On further inspection, the hmac is the base64 decoded string of the `md5` hash with the secret being the given flag. 

I had to find a way to get the output without knowing the secret. I noticed that the `Practice Convo` option gave the `md5` has with the flag as output. And if I give it the same input as the username I entered, I would get the required hmac.

```
Choose an option: 1
Send a message: Bob
Here is your encrypted message: YjRlMGE4MDI0MjhjYjM1ZjY5YzBlOTUyZDk2MTcyZDY=
```

Since the actual hmac is the base64 decoded version of this string, I decoded it to get `b4e0a802428cb35f69c0e952d96172d6`.

```
Choose an option: 2

Bot: Okay, let's see if you're the real deal. What's your name?
Your name: Bob

Bot: Please provide your HMAC
Your HMAC: b4e0a802428cb35f69c0e952d96172d6

Alice: Oh hey Bob! Here is the vault code you wanted:
G0t_Th3_G0ld_B3rl1nale
```

Now that I have the vault password, I can use it to crack the vault.
```
Choose an option: 3

Vault Person: Enter password
Password: G0t_Th3_G0ld_B3rl1nale

Vault Unlocked! The flag is: nite{El_Pr0f3_0f_Prec1s10n_Pl4ns}
```

flag: `nite{El_Pr0f3_0f_Prec1s10n_Pl4ns}`