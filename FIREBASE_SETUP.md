# Firebase Setup Guide

## Autentisering - Vad behöver konfigureras

### 1. Aktivera Email/Password autentisering
1. Gå till Firebase Console: https://console.firebase.google.com/
2. Välj projekt: **packarizer**
3. Gå till `Authentication` → `Sign-in method`
4. Aktivera `Email/Password` (om det inte redan är aktiverat)
5. Spara

### 2. Aktivera Google Sign-In
1. I `Authentication` → `Sign-in method`
2. Aktivera `Google` (om det inte redan är aktiverat)
3. Välj en email som supportemail
4. Spara

## Database Rules - Långsiktig lösning

Uppdatera dina Realtime Database regler för säker, multi-user access:

1. Gå till `Realtime Database` → `Rules`
2. Ersätt de nuvarande reglerna med detta:

```json
{
  "rules": {
    "users": {
      "$uid": {
        ".read": "auth.uid === $uid",
        ".write": "auth.uid === $uid",
        "locations": {
          ".indexOn": ["order"]
        }
      }
    }
  }
}
```

### Vad gör dessa regler?

- **`"$uid"`**: Wild card för user ID
- **`.read`**: Användare kan bara läsa sina egna data (`auth.uid === $uid`)
- **`.write`**: Användare kan bara skriva till sina egna data
- **`.indexOn`**: Optimerar queries på order-fältet (för snabbere sortering)

## Datastruktur

Data lagras per användare:

```
users/
  {uid}/
    locations/
      "Packa inför resor"/
        order: 0
        items/
          "Kudde"/
            active: false
            order: 0
          "Earpods"/
            active: false
            order: 1
      "Arbetsrum"/
        ...
```

## Initial Data Setup

Användaren **stallberg.anders@gmail.com** får automatiskt laddat med alla standardplatser och saker vid första inloggning.

Nya användare får ett tomt system som de kan bygga på själva.

## Testing

1. Öppna `index.html` i en webbläsare
2. Testa att registrera ett nytt konto (kommer vara tom)
3. Logga in med **stallberg.anders@gmail.com** (kommer ha alla saker)
4. Logga ut och logga in igen för att bekräfta att data sparas
5. Testa Google Sign-In

## Säkerhet

✅ Användare kan bara se/ändra sin egen data
✅ Lösenord sparas säkert av Firebase
✅ OAuth via Google är säkert delegerat till Google
✅ Inga tidsrestriktioner på reglerna
✅ Skalbar för många användare
