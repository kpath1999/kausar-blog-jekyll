---
layout: post
title: "SIT-UP: Smart Integrated Technology for Upright Posture"
date: 2024-12-09 15:15:16 17:00:00 +0100
description: my HRI project from FA24
excerpt: A novel approach to posture correction by integrating computer vision with vibratory and auditory feedback, utilizing a height-adjustable standing desk capable of autonomous adjustments.
---
Posture is how you hold your body. Humans did not evolve to sit for hours in unnatural positions working at a desk. As people tire mentally, they pay less attention to their posture. Working for long hours can cause unconscious slouching which reinforces poor postural habits. Beyond poor aesthetics, slouching or slumping can misalign the skeletal system, wear at your spine, cause breathing issues, and many other problems. It is in your best interest to maintain good posture.

**Related work**. Our lifestyles don’t make this any easier. According to a recent study, 80% of the jobs in the U.S. involve prolonged computer or mobile device use. The risks linked to sedentary behavior are now ever present. Numerous posture correction systems have surfaced, including wearable sensors and ‘smart chairs’, but none have taken off. Challenges such as cognitive load and workflow disruptions continue to persist. There is a need for minimally disruptive, adaptive systems that can be integrated into our daily workflows. Our system promotes healthier postural habits without compromising productivity.

**Our system**. SIT-UP has three core components:

* The computer vision system was built using the MediaPipe Pose library. A camera is positioned in front and to the side of the user to capture real-time posture data.
* Vibrations and auditory feedback (ding sounds) act as prompts for users to correct their posture.
* A height adjustable desk equipped with linear actuators and a control unit.

15 participants were recruited and divided into 3 groups:

* Control group: A conventional set-up without any interventions.
* Passive group: They only received vibrations and sound. These were delivered via push notifications, triggering after 10 seconds of continuous postural deviation. The sequencing between the two modalities was randomized to eliminate order effects.
* Active group: The desk was moved up if the user was leaning forward and the desk height was below the maximum threshold. Conversely, the desk moved down if the user was leaning back and the desk height was above the minimum threshold.

Each session lasted for 40 minutes, consisting of a 5-minute calibration period, 30 minutes of the user working on their laptop, and a 5-minute post-session evaluation. During this time, posture data was continuously logged using the CV system. It detected postural deviations using features such as head tilt, shoulder alignment, and spinal curvature. When deviations exceeded a 10-second threshold, one of the interventions – desk, vibration, sound – was triggered.

Computer vision. A dual-camera system uses one camera positioned in front and another at the side. The front camera measures leaning and level misalignment of shoulders. The side camera identifies head posture and slouching. Posture is evaluated every 0.5 seconds and key metrics are logged, including:

* Torso inclination: angle between hip and shoulder in 3D space.
* Spine length: 3D distance between hip and shoulder.
* Neck angle: relative angle between ear, shoulder, and hip coordinates.
* Shoulder level: difference in y-coordinates of the left and right shoulders.
* Upper body lean: difference in mean z-values between ear and shoulder.

Leveraging these parameters, a composite score was calculated to provide an overall assessment of posture: 55 x neck_ + 40 x torso_ + 3 x (1 – lean_) + 2 x (1 – level_). The weights prioritize neck and torso inclinations (55% and 40%, respectively). These have a stronger correlation with postural health. Lean and level are assigned lower weights (3% and 2%, respectively) as they are typically less critical.

The CV system monitors the composite score every 0.5 seconds, extending the bad posture duration if the score falls below 70. Additionally, interventions occur only when bad posture persists beyond a defined threshold and a 30-second cooldown period has elapsed. This prevents overcorrection while also addressing continuous bad posture.

**Desk height adjustment**. The original control board of a commercial standing desk was replaced with an Arduino Nano BLE board and a motor driver. The motor driver uses an H-bridge configuration, which controls the desk’s direction and speed. The Arduino Nano BLE board sends signals to the H-bridge to control the direction of the motor, while the speed is preset using the variable resistor. This setup allows for precise and gradual height adjustments based on the user’s posture.

**Results**. Far from ideal, but only a single user was in the control group. We were not able to recruit too many participants for a pilot study of this nature. This user’s measurements were perturbed and averaged across 5 instances. The composite scores were relatively stable but exhibited a slightly downward trend as time progressed. This decline can be attributed to the natural fatigue associated with prolonged computer use.

The passive group (n=5) exhibited more pronounced variations in posture scores, with lower mean values and wider variance bands compared to the control group. This reflects the diversity in user responsiveness to varying stimuli. Some users appeared highly responsive to auditory cues, while others barely noticed and/or struggled to interpret the feedback.

The individual user graphs reveal deeper insights. In the left panel, the posture score for a specific user showed a noticeable decline during the vibration phase. This user confessed that the vibrations were far too subtle. In contrast, the right panel shows another user with consistently high posture scores. Either they responded well to both modalities or they have naturally great posture. It’s hard to say. This is what makes it challenging to determine which intervention – vibration or sound – is more effective.

Box plots were created for this very purpose. The median score for sound is slightly higher than vibration, suggesting that sound may be marginally more effective at prompting behavior change.

With the active group (n=5), the mean was slightly higher, and the variance bands were narrower than the passive group. The desk height adjustments were more effective than the passive interventions. It turns out that the mean score was lower than the control group likely due to the novel medium and/or user differences.

**Qualitative feedback**. A post-session questionnaire, adapted from the User Experience Questionnaire (UEQ), assessed effectiveness, intelligence, comfort, and fatigue with the SIT-UP system. Users in both the passive and active groups perceived the system as significantly more effective and intelligent compared to those in the control group. The productivity score showed minimal improvement, meaning users did not gain any superpowers while sitting upright. Comfort, fatigue and intrusiveness were rated less favorably in both intervention groups.

**Statistical tests**. 4 hypotheses were tested comparing mean composite scores during and after passive and active interventions to baseline. Wilcoxon paired statistical comparisons were conducted.

_tl;dr_: No statistically significant conclusions. Here's a rhyme:

__Beeps and buzzes, a posture quest,
Stats said "Meh" to our behest.
P-values danced, but none impressed,
Our backs remained a slouchy mess.
Sound's 0.4, vibe's 0.1,
No winner in this spinal fun.
Post-zaps? 0.06 and 0.8,
Still no straight spine to celebrate.
In science's quest for perfect stance,
These numbers led us on a dance.
The moral? Sometimes, with a shrug,
We learn our hypothesis needs a hug.__

While p-values differed slightly, no statistically significant conclusions could be made for any of the hypotheses. The study highlights the complexity of posture intervention and the need for further research in this area.

**Limitations**. A major challenge was defining “good posture,” which varies among individuals. It is hard to mathematically codify and requires careful calibration based on the person being studied. A few study design constraints crept up too. Lack of within-comparison testing limited the direct comparison of participant responses. Participants may “try too hard” due to the awareness of being studied (Hawthorne effect). Script interruptions occurred occasionally due to a power supply issue, which were mitigated by resuming the study as soon as a team member found out. The vibration feedback method (using a phone instead of integrating it into a chair) was not ideal. The sample size was too small, especially the single-person control group. And lastly, the short-term nature of this study was a key drawback. Behavioral shifts only become more apparent if the system’s impact is assessed over a longer horizon, say, a month.

**Synthesizing it all**. If we learned anything, it is that building a posture correction system is complicated. Without intervention, posture tends to worsen over time, emphasizing the need for some external support. Passive interventions (sounds or vibrations) show mixed results. Some people respond better to certain types of reminders, suggesting that a one-size-fits-all approach may not work for everyone. A personalized, adaptive system could be more effective. Active desk adjustments seem to work better. However, frequent desk adjustments, while helpful, may mess with a user’s concentration. The key takeaway is that future posture correction systems should aim to balance effectiveness with user comfort.

**Coda**. This project was conducted as part of the Human-Robot Interaction class at Georgia Tech. Dr. Sonia Chernova supervised this project, and her TAs, Maithili and Karthik, helped facilitate it.