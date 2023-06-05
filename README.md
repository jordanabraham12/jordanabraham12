# Assurez-vous d'avoir Django installé (pip install django)

from django.db import models
from django.contrib.auth.models import User

# Modèle pour stocker les informations du compte bancaire
class BankAccount(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    account_number = models.CharField(max_length=20)
    balance = models.DecimalField(max_digits=10, decimal_places=2)

    def deposit(self, amount):
        self.balance += amount
        self.save()

    def withdraw(self, amount):
        if amount <= self.balance:
            self.balance -= amount
            self.save()
        else:
            raise ValueError("Insufficient balance")

# Exemple de vue pour effectuer un dépôt sur un compte
from django.shortcuts import render, redirect

def deposit_view(request):
    if request.method == 'POST':
        amount = float(request.POST['amount'])
        account = BankAccount.objects.get(user=request.user)
        account.deposit(amount)
        return redirect('dashboard')  # Rediriger vers la page de tableau de bord
    else:
        return render(request, 'deposit.html')

# Exemple de modèle de formulaire pour le dépôt
from django import forms

class DepositForm(forms.Form):
    amount = forms.DecimalField(label='Montant du dépôt')

# Exemple de template HTML pour le formulaire de dépôt
<!-- deposit.html -->
<form method="post" action="{% url 'deposit' %}">
    {% csrf_token %}
    {{ form }}
    <button type="submit">Déposer</button>
</form>
