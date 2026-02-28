# Χρησιμοποιούμε μια επίσημη εικόνα Python
FROM python:3.10-slim

# Ορίζουμε το φάκελο εργασίας μέσα στο container
WORKDIR /app

# Αντιγράφουμε το requirements.txt για να εγκαταστήσουμε τις βιβλιοθήκες
COPY requirements.txt .

# Εγκατάσταση των απαραίτητων βιβλιοθηκών
RUN pip install --no-cache-dir -r requirements.txt

# Αντιγράφουμε τον κώδικα (notebooks, κτλ) στο container
COPY . .

# Ανοίγουμε την πόρτα για το Jupyter Notebook
EXPOSE 8888

# Εντολή για να ξεκινάει το Jupyter μόλις τρέξει το container
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]