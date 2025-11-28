No Bash (pythonanywhere):

Use workon vEnv (ou venv) antes de fazer as próximas instruções

Se precisar baixar bibliotecas:
pip install flask
pip install flask-bootstrap
pip install flask-mail
pip install flask-moment
pip install flask-wtf
pip install requests
pip install flask-migrate
pip install flask-sqlalchemy
pip install email_validator
pip install flask-login

Comandos para banco de dados no bash:
export FLASK_APP=flasky.py
flask shell
from flasky import db
db.create_all()
db.drop_all()
db.create_all()
from flasky import Role, User
adm_role = Role(name=’Administrator’)
user_role = Role(name=’User’)
db.session.add_all([adm_role, user_role])
db.session.commit()

(crtl + D para sair do flask shell)

Se precisar inserir usuários no banco de dados pelo bash:
flask shell
from flasky import Role, User
adm_role = Role.query.filter_by(name=’Administrator’).first()
user_role = Role.query.filter_by(name=’User’).first()
usuarioAdministrador = User(username=’Susana’, role=adm_role)
usuarioUsuario = User(username=’Mariana’, role=user_role)

Para pegar os arquivos do github e depois conectar no seu novo repositório:
chmod 777 change_repo.sh
./change_repo.sh linkdorepositorio.git
git init
git remote set-url origin linkdomeurepositorio.git

(retire todas as informações do .env antes de continuar!!!)

git add .
git commit -m “arquivos”
git push -u origin master
(vai precisar do nick do github e token)

Para trazer as alterações feitas no repositório do github pro pythonanywhere:
git pull 
(se não funcionar: git pull origin master)

Se quiser substituir tudo que tiver localmente pelo que está no github:
git reset –hard origin/master
(são dois traços antes de hard!)


pra guardar, por exemplo, as funções em ordem alfabética, dá pra fazer o seguinte antes de mandar as funções pro template:
funcoes = Role.query.order_by(Role.name).all()

aí quando você for usar no template, na parte da tabela você vai percorrer todo o vetor, e como você guardou as funções na ordem alfabética, elas vão estar nessa ordem na tabela também

<h2>Relação de funções cadastradas</h2>
    {% if usuarios %}
        <table class='table'>
            <thead>
                <th>Funções</th>
                <th>Usuários</th>
            </thead>
            <tbody>
                {% for i in funcoes %}
                    <tr>
                        <td>{{ i.name }}</td>
                        <td>
                            <ol>
                                {% for j in i.users.all() %}
                                    <li>{{ j.username }}</li>
                                {% endfor %}
                            </ol>
                        </td>
                    </tr>
                {% endfor %}
            </tbody>
        </table>
    {% endif %}


pra criar uma nova função é sempre no bash do pythonanywhere

export FLASK_APP=flasky.py
flask shell
from flasky import db
db.create_all()
 _db.drop_all()_
_db.create_all()_ 
from flasky import Role, User
adm_role = Role(name='Administrator')
user_role = Role(name='User')

pra criar um usuário pelo bash, usando essas funções que você acabou de criar, é assim:

usuario = User(username='Susana', role=user_role)

usuarioAdm = User(username='Susana', role=adm_role)

Pra preencher o "role" do usuário, eu usei uma variável que guarda o objeto (função) que eu tinha criado (adm_role ou user_role)

Se você já tinha as funções criadas, pra guardar uma delas numa variável você usa consulta:

adm_role = Role.query.filter_by(name='Administrator').first()


pra criar novos campos de formulário, você edita a classe de formulário que você está usando na sua função que chama o template
Exemplo:
- função:
@app.route('/', methods=['GET','POST'])
def index():
    form = NameForm() ⚠️⚠️⚠️
    if form.validate_on_submit():
        user = User.query.filter_by(username=form.name.data).first()
        if user is None:
            user_role = Role.query.filter_by(name = form.role.data).first()
            user = User(username=form.name.data, role = user_role)
            db.session.add(user)
            db.session.commit()
            session['known'] = False
        else:
            session['known'] = True
        session['name'] = form.name.data
        return redirect(url_for('index'))
    usuarios = User.query.all()
    funcoes = Role.query.order_by(Role.name).all()
    qtdUsuarios = User.query.count()
    qtdFuncoes = Role.query.count()
    return render_template('index.html', form=form, nome=session.get('name'), known=session.get('known', False), usuarios = usuarios, funcoes = funcoes, qtdUsuarios = qtdUsuarios, qtdFuncoes = qtdFuncoes)

O que vai definir os campos do formulário vai ser o que tiver na classe do formulário (nesse caso, a função que mostrei antes está criando um da classe NameForm)

- classe:

class NameForm(FlaskForm):
    name = StringField("What is your name?", validators = [DataRequired()])
    role = SelectField("Role?:", choices = [('Administrator', 'Administrator'), ('Moderator', 'Moderator'), ('User', 'User')], validators = [DataRequired()])
    submit = SubmitField('Submit')

Daí, no template usamos uma função do wtf forms pra criar automaticamente o formulário com todos os campos que tiver nele:

{{ wtf.quick_form(form) }}

pra você ver onde isso está no template, a atividade que eu olhei pra me basear foi essa aqui:
https://github.com/DesSistemasWeb-Servidor/Ativ8_BancoDeDados/blob/master/templates/index.html


você pegaria o link do repositório da atividade que você achou parecida, usaria chmod 777 change_repo.sh, depois ./change_repo.sh linkdesserepositório

Depois disso, você usaria git remote set-url origin linkdoseurepositórionovo
git add .
git commit -m "atualizações"
git push -u origin master