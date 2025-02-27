# 🚨 Lidando com Tracebacks no Django e Django REST Framework 🚨

## 📌 Introdução
Erros no terminal (tracebacks) são mensagens detalhadas que ajudam a entender onde e por que o código falhou. No Django e Django REST Framework (DRF), os erros geralmente estão relacionados a problemas de código, configurações ou dependências. Vamos explorar os mais comuns e como resolvê-los.

---

## 1️⃣ `ModuleNotFoundError: No module named 'django'`
**Causa:** O Django não está instalado no ambiente virtual.

**Solução:**
```sh
source venv/bin/activate  # Mac/Linux
venv\Scripts\activate  # Windows
pip install django
```

---

## 2️⃣ `ImportError: cannot import name '...' from '...'`
**Causa:** Nome do arquivo ou módulo incorreto, ou ciclo de importação.

**Solução:**
- Evite conflitos de nomes (ex: `django.py`).
- Se for um problema de importação circular, importe dentro da função onde será utilizado.

---

## 3️⃣ `TypeError: 'function' object is not callable`
**Causa:** Esqueceu de adicionar `()` ao chamar uma função.

**Solução:**
```python
def saudacao():
    return "Olá, mundo!"

print(saudacao)  # ❌ Erro!
print(saudacao())  # ✅ Correto
```

---

## 4️⃣ `SyntaxError: invalid syntax`
**Causa:** Erro de digitação no código.

**Solução:**
- Verifique `:` em funções, loops e condicionais.
- Feche corretamente aspas e parênteses.

---

## 5️⃣ `AttributeError: 'NoneType' object has no attribute '...'`
**Causa:** Tentativa de acessar um atributo de um objeto `None`.

**Solução:**
```python
usuario = None
print(usuario.nome)  # ❌ Erro!

usuario = User.objects.first()
if usuario:
    print(usuario.nome)  # ✅ Correto
```

---

## 6️⃣ `django.core.exceptions.ImproperlyConfigured: The SECRET_KEY setting must not be empty.`
**Causa:** O `SECRET_KEY` não está definido no `settings.py`.

**Solução:**
```python
SECRET_KEY = 'sua-chave-secreta-aqui'
```

---

## 7️⃣ `django.db.utils.OperationalError: no such table: auth_user`
**Causa:** O banco de dados ainda não foi migrado.

**Solução:**
```sh
python manage.py migrate
```

---

## 8️⃣ `TypeError: Object of type QuerySet is not JSON serializable`
**Causa:** Tentativa de retornar um `QuerySet` diretamente em uma view sem converter para JSON.

**Solução:**
```python
from django.http import JsonResponse
from .models import Usuario

def listar_usuarios(request):
    usuarios = Usuario.objects.all().values()
    return JsonResponse(list(usuarios), safe=False)  # ✅ Correto
```

---

## 9️⃣ `django.urls.exceptions.NoReverseMatch: '...' is not a valid view function or pattern name`
**Causa:** O nome da URL especificado no `reverse()` não corresponde a um nome registrado.

**Solução:**
```python
from django.urls import reverse
reverse('perfil', kwargs={'id': 1})  # ✅ Correto
```

---

## 💡 Outros Erros Comuns

### `KeyError: '...'`
**Causa:** Tentativa de acessar uma chave inexistente em um dicionário.
```python
dados = {"nome": "Alice"}
print(dados.get("idade", "Idade não informada"))  # ✅ Correto
```

### `django.core.exceptions.FieldError: Invalid field name(s) given in select_related`
**Causa:** Tentativa de usar um campo inexistente em `select_related()`.

### `django.db.utils.IntegrityError: NOT NULL constraint failed`
**Causa:** Tentativa de criar um objeto sem preencher um campo obrigatório.
```python
class Usuario(models.Model):
    email = models.CharField(max_length=255, null=True)  # ✅ Correto
```

### `django.core.exceptions.ImproperlyConfigured: The included app '...' does not appear to be installed`
**Causa:** O app criado ou o `rest_framework` não está adicionado ao `INSTALLED_APPS`.

**Solução:**
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'meu_app',  # ✅ Certifique-se de adicionar seu app aqui
    'rest_framework',  # ✅ Se estiver usando Django REST
]
```

---

## 👩‍💻 Dicas para Debugging
✅ Leia o traceback **de baixo para cima**.
✅ Verifique **se há erro de sintaxe ou importação**.
✅ Use **`print()`** ou `pdb` para debugar.
✅ Consulte a **documentação do Django e do DRF** para erros comuns.

---
