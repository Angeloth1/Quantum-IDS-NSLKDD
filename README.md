# ⚛️ Quantum Intrusion Detection System (QIDS)

Αυτό το αποθετήριο (repository) περιέχει τον πηγαίο κώδικα και τα πειραματικά δεδομένα της πτυχιακής έρευνας με θέμα την αξιολόγηση της **Κβαντικής Μηχανικής Μάθησης (Quantum Machine Learning - QML)** για την ανίχνευση δικτυακών εισβολών. 

Η έρευνα αξιολογεί τα όρια των Variational Quantum Classifiers (VQC) στο περιβάλλον των σύγχρονων Noisy Intermediate-Scale Quantum (NISQ) συστημάτων, χρησιμοποιώντας το αυστηρό benchmark του **NSL-KDD dataset**.

---

## 📑 Πίνακας Περιεχομένων
1. [🎯 Σκοπός της Έρευνας](#-σκοπός-της-έρευνας)
2. [🛠️ Αρχιτεκτονική & Μεθοδολογία](#️-αρχιτεκτονική--μεθοδολογία)
3. [📊 Βασικά Ευρήματα (Key Findings)](#-βασικά-ευρήματα-key-findings)
4. [💻 Τεχνολογικό Stack](#-τεχνολογικό-stack)
5. [🚀 Οδηγίες Εγκατάστασης (Local Setup)](#-οδηγίες-εγκατάστασης-local-setup)

---

## 🎯 Σκοπός της Έρευνας
Ο κύριος στόχος είναι η διερεύνηση της πρακτικής αποδοτικότητας των κβαντικών αλγορίθμων στην ταξινόμηση κακόβουλης δικτυακής κίνησης. Η έρευνα εστιάζει στην **"Κβαντική Οικονομία" (Quantum Economy)**: πώς μπορούμε να συμπιέσουμε μαθηματικά την πληροφορία ενός πολύπλοκου dataset, ώστε να εκτελεστεί σε περιορισμένα Qubits, χωρίς να καταστραφεί η γραμμική διαχωρισιμότητα των δεδομένων.

---

## 🛠️ Αρχιτεκτονική & Μεθοδολογία
Η στρατηγική του pipeline μας σχεδιάστηκε για να μετριάσει το "Quantum Bottleneck" και περιλαμβάνει:

* **Αυστηρό Data Preprocessing:** Καθαρισμός και διπλό Stratified Split (βάσει Label & `src_bytes` - Μεγέθους Πακέτου) για αποτροπή distribution shift.
* **Leakage-Free Feature Engineering:** Αντικατάσταση του One-Hot Encoding με **Target Encoding** αποκλειστικά στο Training Set, μειώνοντας την αραιότητα (sparsity) των πινάκων.
* **Dimensionality Reduction (PCA-16):** Εξαγωγή ακριβώς 16 κύριων συνιστωσών (διατηρώντας το 86.78% της διακύμανσης).
* **Κβαντική Αρχιτεκτονική:**
    * **Amplitude Encoding:** Ενσωμάτωση των 16 χαρακτηριστικών σε μόλις **4 Qubits** ($2^4=16$).
    * **Ansatz:** Χρήση του `RealAmplitudes` (reps=5) με 24 εκπαιδεύσιμες παραμέτρους.
* **Στρατηγική Βελτιστοποίησης:** Συγκριτική αξιολόγηση μεταξύ ντετερμινιστικών (COBYLA) και στοχαστικών, ανθεκτικών στον θόρυβο (SPSA) optimizers για την αποφυγή των Barren Plateaus.

---

## 📊 Βασικά Ευρήματα (Key Findings)
Τα πειράματα κατέδειξαν με σαφήνεια τα όρια της Κωδικοποίησης Πλάτους (Amplitude Encoding) στα σημερινά NISQ συστήματα:

1.  **Δυαδική Ταξινόμηση (Binary Classification):** Το 4-qubit VQC πέτυχε ένα σταθερό **88% Accuracy**, λειτουργώντας ως ένα ικανοποιητικό proof-of-concept.
2.  **Κλασικό Benchmark (The Bottleneck):** Ένα κλασικό, αυστηρά περιορισμένο "Tiny MLP" με μόλις 4 κόμβους στο hidden layer, ξεπέρασε το VQC επιτυγχάνοντας **97%**. Αυτό αποδεικνύει ότι τα δεδομένα διατηρούν τη σημασιολογία τους, αλλά τα κβαντικά μοντέλα δυσκολεύονται λόγω των αποκλειστικά γραμμικών, μοναδιαίων (unitary) μετασχηματισμών τους.
3.  **Πολυταξική Ταξινόμηση (Multiclass Mode Collapse):** Σε ένα απόλυτα ισορροπημένο dataset 4 κλάσεων επιθέσεων, η ακρίβεια του VQC κατέρρευσε στο **9%** (κάτω από το τυχαίο 25%). Αυτό αποδεικνύει ότι η υπερ-συμπίεση πολλαπλών χαρακτηριστικών σε 4 qubits καταστρέφει ανεπανόρθωτα τη γεωμετρία διαχωρισμού σύνθετων επιθέσεων.

---

## 💻 Τεχνολογικό Stack
* **Γλώσσα:** Python 3.10+
* **Κβαντικό Framework:** IBM Qiskit (Aer Simulator, Machine Learning, Algorithms)
* **Data Science:** Scikit-Learn, Pandas, NumPy
* **Οπτικοποίηση:** Matplotlib, Seaborn

---

## 🚀 Οδηγίες Εγκατάστασης (Local Setup)

Το project εκτελείται ιδανικά σε περιβάλλον Linux, κάνοντας χρήση Python Virtual Environments.

### 1. Λήψη του Dataset
Λόγω μεγέθους, το NSL-KDD dataset δεν περιλαμβάνεται στο repo. 
* Κατεβάστε το από το [University of New Brunswick (UNB)](https://www.unb.ca/cic/datasets/nsl.html).
* Τοποθετήστε τα αρχεία `.txt` (π.χ. `KDDTrain+.txt`, `KDDTest+.txt`) στον φάκελο `data/`.

### 2. Κλωνοποίηση & Εγκατάσταση
Ανοίξτε το terminal σας και εκτελέστε:

```bash
# Κλωνοποίηση του αποθετηρίου
git clone [https://github.com/Angeloth1/Quantum-IDS-NSLKDD.git](https://github.com/Angeloth1/Quantum-IDS-NSLKDD.git)
cd Quantum-IDS-NSLKDD

# Δημιουργία και ενεργοποίηση Virtual Environment (προαιρετικό αλλά προτεινόμενο)
python -m venv venv
source venv/bin/activate

# Εγκατάσταση εξαρτήσεων
pip install -r requirements.txt
