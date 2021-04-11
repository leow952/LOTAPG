# LOTAPG - localization of the admin panel django

Добовляем мультиязычность в штатную админ панель django
![image](https://github.com/leow952/LOTAPG/blob/main/Скриншот_Выберите%20область_20210411124108.png)

# 1 settings.py

    MIDDLEWARE = [
        ...
        'django.middleware.locale.LocaleMiddleware',
    ]

    LANGUAGE_CODE = 'ru'  # язык сайта по умолчанию

    LANGUAGES = (
        ('ru', 'Russian'),
        ('kk', 'Kazakh'),
        ('en', 'English'),
        ...
    ) # языки которые будут использоваться

    LOCALE_PATHS = (os.path.join(PROJECT_DIR, 'locale'),) # место хранения переводов

    TIME_ZONE = 'Europe/Moscow'

    USE_I18N = True
    USE_L10N = True
    USE_TZ = True


# 2 urls.py

Добавить 

    from django.conf.urls.i18n import i18n_patterns

    urlpatterns += i18n_patterns(
        path('admin/', admin.site.urls),
    )


# 3 templates/admin/base.html

копируем штатный шаблон и добовляем в него кнопки и скрипт

прикладываю шаблон(Python 3.7.3, Django 3.1.7)

    <ul style="display: flex; justify-content: flex-end" class="langswitcher">
        <li style="list-style-type: none; margin-left: 5px"><a href="#ru" data-lang="ru">Ru</a></li>
        <li style="list-style-type: none; margin-left: 5px"><a href="#en" data-lang="en">En</a></li>
        <li style="list-style-type: none; margin-left: 5px"><a href="#kk" data-lang="kk">Kz</a></li>
    </ul>
    
    <script src="https://code.jquery.com/jquery-3.6.0.min.js" integrity="sha256-/xUj+3OJU5yExlq6GSYGSHk7tPXikynS7ogEvDej/m4=" crossorigin="anonymous"></script>
    <script>
        $(".langswitcher a").map(function (i, el) {
         if ( $(el).attr('data-lang') === window.location.pathname.slice(1, 3)){
            $("html").attr("lang",$(this).data("lang"));
            $(el).css('color', '#79aec8')
         }
         })
        $(".langswitcher a").on("click",function(e){
            document.location.assign(window.location.protocol + '//' + window.location.host + '/' + $(e.target).attr('data-lang') + window.location.pathname.slice(3))
        });
    </script>


# 4 Перевод в models.py

    from django.utils.translation import ugettext_lazy as _


    class User(AbstractUser):
        bio = models.TextField(_('Биография'), max_length=500, blank=True)
        birth_date = models.DateField(_('День рождения'), null=True, blank=True)
        class Meta:
            verbose_name = _('Пользователь')
            verbose_name_plural = _('Пользователи')


# 5 Генерируем файлы переводов

Для создания папки с словами переводов выполним команду в терминале 
где en название языка из настроек а venv папка с виртуальным окружением

    python manage.py makemessages -l en -i venv

после выполнения команды появися locale/en/LC_MESSAGES/django.po

после заполнения файлов перевода django.po добавим их в систему командой

    python manage.py compilemessages



