###### What is this project about?



We built a model that looks at a person's face and tells you what emotion they're feeling — angry, disgust, fear, happy, neutral, sad, or surprise. Just from a photo, no text, no questions asked.



**What data did we use?**



We got a folder of \~36,000 tiny face photos (48x48 pixels, black \& white), already sorted into 7 folders — one folder per emotion. Like someone already labeled thousands of faces for us: "this one is happy," "this one is sad," etc.



\~28,700 photos to teach the model (train folder)

\~7,200 photos to test if it actually learned (validation folder)



**Step 1 — EDA**



Before building anything, we looked at the data closely, We found:



**Imbalance:** "happy" had 7,164 photos, but "disgust" had only 436. If we ignore this, the model would get lazy and mostly guess "happy" every time, since it saw that face the most.



**Duplicates:** about 5% of images were exact copies of each other (same photo saved twice).

&#x20;

* We looked at actual sample faces and found some emotions look very similar (like fear and surprise — both have wide eyes and open mouth), so we knew upfront this would be a

&#x20;  genuinely hard problem, not something that hits 99% accuracy.



**Step 2 — Preparing the data**



A few simple prep steps, like getting vegetables ready before cooking:



* Converted pixel brightness (0-255) into a 0-to-1 scale — models learn faster with small numbers.
* Told the model to treat "disgust" mistakes as more serious than "happy" mistakes (called class weights) — this stops it from ignoring the rare emotion.
* Made small random variations of training photos (tilt, flip, zoom) — called augmentation — so the model sees more variety and doesn't just memorize the exact same 28,700 photos.



**Step 3 — Building 3 different models (and comparing them)**



We didn't just build one model — we built 3 and let the results decide the winner, like trying 3 recipes and picking the tastiest.



Model 1 — Baseline CNN: A CNN (Convolutional Neural Network) built completely from scratch. Think of CNN as a system that scans small patches of the image (like eyes, mouth corners) and

&#x20;        learns which patch-patterns mean which emotion.



Model 2 — Transfer Learning (MobileNetV2): Instead of starting from zero, we borrowed a model that Google already trained on millions of general photos (cars, animals, objects — not faces

&#x20;         specifically), and just taught its last layer to focus on emotions. Like hiring someone who already knows how to "see" well, and just teaching them your specific task.



Model 3 — Fine-tuned version of Model 2: We let that borrowed model adjust a bit more of itself to faces specifically, not just the last layer.



|Model|Accuracy|Balanced Score (Macro F1)|
|-|-|-|
|Model 1 (from scratch)|51.2%|0.450|
|Model 2 (transfer learning)|49.6%|0.482 ← winner|
|Model 3 (fine-tuned)|45.3%|0.430|



**Interesting twist:**



Model 1 had slightly higher raw accuracy, but Model 2 was more balanced across all 7 emotions (not just good at the easy ones like happy). So Model 2 was picked as the final model.



Also — fine-tuning (Model 3) actually made things worse, not better. That's a real, honest finding, not a mistake to hide.



**Accuracy means:** “How many predictions did the model get right?”



The problem is when one category is much bigger than another.



For example:



90 happy photos

10 disgust photos



If the model predicts “happy” for almost everything, it can still get high accuracy (around 90%) even though it's bad at recognizing disgust.





**Macro F1 means:** “How well is the model doing on each emotion, giving every emotion equal importance?”



It calculates an F1 score for each emotion, then takes the average.

So even if happy has many photos, disgust still gets the same importance.



**Step 5 — Explainability (Grad-CAM)**



We used a technique called Grad-CAM that draws a heatmap on the face showing where the model was looking when it made its decision — ideally the eyes/mouth, not the background hair or wall. This builds trust that the model is actually learning real facial cues, not cheating with some unrelated shortcut.



**Step 6 — Testing on totally new photos**



Finally, we tested the saved model on 3 fresh photos it had never seen (your own portrait photos) — this mimics a real "camera takes your photo, model reacts" scenario. It leaned toward predicting "happy" on all 3, which we traced back to a real reason: those were full portraits (with background/hair), not tightly-cropped faces like the training data — a legit, explainable limitation, not randomness.



So in one line we can say that We took 36,000 labeled face photos, taught 3 different neural networks to recognize 7 emotions from them, picked the most balanced one (transfer-learning MobileNetV2).

