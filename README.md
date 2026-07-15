# Adversarial Attack & Defense on ML-Based Network Intrusion Detection

## Developer
Christian de Guzman

## Purpose
Create a machine learning model that detects network instructions (cyberattacks in network traffic). Attack the model itself using adversarial techniques and then fix it <br>
This project is to help improve my understanding of AI/ML. Also, providing an opportunity to grow as a developer by combining my passion for both Computer Science and Cybersecurity.

## Objectives
Create a Threat Detector by training a neural network on real-world labeled network traffic (CCIDS2017 dataset) to classify traffic as benign or malicious. <br>
Simulate a real attacker (Adversarial Attacker) by using FGSM and PGD attacks to craft malicious network traffic that the model missclassifies as safe. <br>
Retrain the model using adversarial training (feeding it attack examples during training) and apply input preprocessing defenses. 

## What I learned
- I learned how to run jupyter notebook from Visual Studio Code terminal. I usually run jupyter notebook on Google Drive.
- I learned that I had to only take 30% of each file which is still hundreds of thousands of rows. The full dataset is too big for my current RAM. 
- 


## Resources and Works Cited
- Iman Sharafaldin, Arash Habibi Lashkari, and Ali A. Ghorbani, “Toward Generating a New Intrusion Detection Dataset and Intrusion Traffic Characterization”, 4th International Conference on Information Systems Security and Privacy (ICISSP), Portugal, January 2018.
- Adversarial Robustness Tutorial: FGSM vs PGD Attacks in PyTorch (Hands-on Code): https://www.youtube.com/watch?v=uX7IkTlyfuc
- Adversarial Robustness Toolbox How to attack and defend your machine learning models: https://www.youtube.com/watch?v=tTN_BqTv440
