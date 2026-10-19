# TryHackMe: Letter

- **Level:** Easy
- **Flag format:** `THM{Name_Surname_age}` — only the first letter of the first and last name should be capitalized.

## 1. Analyzing the files

I started by unzipping the `.zip` archive. It contains two `.png` images, a text file, and another folder.

### The text file

> Mon cher Édouard,
>
> Aujourd'hui, en rangeant le grenier chez mes grands-parents, je suis tombée sur cette vieille coupure de journal. Ton arrière-grand-père n'avait même pas l'âge de passer le permis quand il s'est distingué ce jour-là. Le benjamin de l'équipe, et certainement pas le moins courageux.
>
> Il serait si fier de te voir sur l'eau à ton tour.
>
> Avec toute mon affection,
> Audette

*Translation: a grandmother named Audette writes to her grandson Édouard, telling him she found an old newspaper clipping. His great-great-grandfather was not even old enough to get a driver's license when he distinguished himself that day — the youngest member of the team, and certainly not the least courageous.*

At first sight, nothing of interest, apart from two names: **Édouard** and **Audette**.

### The images

At first glance, there is nothing of interest either; one can only note that both the letter and the newspaper come from France. I tried steganography tools like `binwalk` and `steghide` to look for clues hidden in the photos, but nothing turned up.

## 2. The SNSM lead
<img width="1448" height="1086" alt="image" src="https://github.com/user-attachments/assets/ce840291-8806-4285-8499-f7679b82d94b" />

When I analyzed the letter again in greater detail, I noticed the initials **SNSM** on the envelope. A quick search leads to the *Société Nationale de Sauvetage en Mer* (French National Society for Sea Rescue), and more precisely to the **Penmarc'h** station in Finistère, Brittany (postal code 2\*\*\*\*).

## 3. Historical context: May 23, 1925
<img width="1021" height="397" alt="image" src="https://github.com/user-attachments/assets/03ae6859-fc79-430a-8441-3592685a744b" />

Regarding the flag, an analysis of the logbook reveals a note along the lines of: "Did Amund reach the North Pole?" A brief search points us to **Roald Amundsen**, who reached the North Pole in May 1926 aboard the airship *Norge*. It is also worth noting that the letter mentions the youngest member of the team.
Furthermore, the logbook contains the French phrase "une c...phe sur .... du Finistère" ("a c...phe off the coast of Finistère").

The historical context points to May 23, 1925—a day when French and international news was dominated by two major events that made headlines:

1. **The tragedy of the Penmarc’h shipwreck:** A violent storm struck the tip of Finistère. Rescue boats from the local SNSM (French National Society for Sea Rescue) station capsized while attempting to assist fishermen, resulting in the deaths of 27 Breton rescuers and sailors.
2. **Anxiety surrounding Amundsen’s expedition to the North Pole:** Having departed from Spitsbergen on May 21, 1925, in two seaplanes (the N-24 and N-25), the Norwegian explorer Roald Amundsen had ceased all communication. The entire world, hanging on every dispatch transmitted via wireless telegraphy (TSF), feared that he had crashed onto the polar ice pack.
## 4. Flag

I obtained the flag thanks to a memorial website which contains an article about the disaster; the youngest member of the team is mentioned there:

```
THM{Yves-Marie_Gourl******_15}
```
