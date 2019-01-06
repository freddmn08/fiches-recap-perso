# Création d'un formulaire de login

[doc How to build a login form](https://symfony.com/doc/current/security/form_login_setup.html)

## 1. Préalable

S'assurer que l'on a bien installé le composant `security` et que l'entité `User` a bien été créée (cf. fiche [Security](docs/symfony/security/security.md).

## 2. Génération du formulaire de login

On crée le formulaire d'authentification avec la commande `make:auth` du `MakerBundle` :

```
php bin/console make:auth

What style of authentication do you want? [Empty authenticator]:
 [0] Empty authenticator
 [1] Login form authenticator
> 1

The class name of the authenticator to create (e.g. AppCustomAuthenticator):
> LoginFormAuthenticator

Choose a name for the controller class (e.g. SecurityController) [SecurityController]:
> SecurityController

 created: src/Security/LoginFormAuthenticator.php
 updated: config/packages/security.yaml
 created: src/Controller/SecurityController.php
 created: templates/security/login.html.twig
 ```

 Cela génère trois choses :

* (1) une route `/login` et le contrôleur `LoginController` associé

    <details><summary>Code de la route et du contrôleur</summary>
    
    ```
    // src/Controller/SecurityController.php
    namespace App\Controller;

    use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
    use Symfony\Component\HttpFoundation\Response;
    use Symfony\Component\Routing\Annotation\Route;
    use Symfony\Component\Security\Http\Authentication\AuthenticationUtils;

    class SecurityController extends AbstractController
    {
        /**
        * @Route("/login", name="app_login")
        */
        public function login(AuthenticationUtils $authenticationUtils): Response
        {
            // get the login error if there is one
            $error = $authenticationUtils->getLastAuthenticationError();
            // last username entered by the user
            $lastUsername = $authenticationUtils->getLastUsername();

            return $this->render('security/login.html.twig', [
                'last_username' => $lastUsername,
                'error' => $error
            ]);
        }
    }
    ```
    </details>

* (2) un template `login` affichant le formulaire de login

    <details><summary>Code du template</summary>
    
    ```
    {% extends 'base.html.twig' %}

    {% block title %}Log in!{% endblock %}

    {% block body %}
    <form method="post">
        {% if error %}
            <div class="alert alert-danger">{{ error.messageKey|trans(error.messageData, 'security') }}</div>
        {% endif %}

        <h1 class="h3 mb-3 font-weight-normal">Please sign in</h1>
        <label for="inputEmail" class="sr-only">Email</label>
        <input type="email" value="{{ last_username }}" name="email" id="inputEmail" class="form-control" placeholder="Email" required autofocus>
        <label for="inputPassword" class="sr-only">Password</label>
        <input type="password" name="password" id="inputPassword" class="form-control" placeholder="Password" required>

        <input type="hidden" name="_csrf_token"
            value="{{ csrf_token('authenticate') }}"
        >

        {#
            Uncomment this section and add a remember_me option below your firewall to activate remember me functionality.
            See https://symfony.com/doc/current/security/remember_me.html

            <div class="checkbox mb-3">
                <label>
                    <input type="checkbox" name="_remember_me"> Remember me
                </label>
            </div>
        #}

        <button class="btn btn-lg btn-primary" type="submit">
            Sign in
        </button>
    </form>
    {% endblock %}
    ```
    </details>

* (3) une classe `Guard authenticator` traitant le login submit

    <details><summary>Code de la classe</summary>
    
    ```
        // src/Security/LoginFormAuthenticator.php
    namespace App\Security;

    use App\Entity\User;
    use Doctrine\ORM\EntityManagerInterface;
    use Symfony\Component\HttpFoundation\RedirectResponse;
    use Symfony\Component\HttpFoundation\Request;
    use Symfony\Component\Routing\RouterInterface;
    use Symfony\Component\Security\Core\Authentication\Token\TokenInterface;
    use Symfony\Component\Security\Core\Encoder\UserPasswordEncoderInterface;
    use Symfony\Component\Security\Core\Exception\InvalidCsrfTokenException;
    use Symfony\Component\Security\Core\Security;
    use Symfony\Component\Security\Core\User\UserInterface;
    use Symfony\Component\Security\Core\User\UserProviderInterface;
    use Symfony\Component\Security\Csrf\CsrfToken;
    use Symfony\Component\Security\Csrf\CsrfTokenManagerInterface;
    use Symfony\Component\Security\Guard\Authenticator\AbstractFormLoginAuthenticator;
    use Symfony\Component\Security\Http\Util\TargetPathTrait;

    class LoginFormAuthenticator extends AbstractFormLoginAuthenticator
    {
        use TargetPathTrait;

        private $entityManager;
        private $router;
        private $csrfTokenManager;
        private $passwordEncoder;

        public function __construct(EntityManagerInterface $entityManager, RouterInterface $router, CsrfTokenManagerInterface $csrfTokenManager, UserPasswordEncoderInterface $passwordEncoder)
        {
            $this->entityManager = $entityManager;
            $this->router = $router;
            $this->csrfTokenManager = $csrfTokenManager;
            $this->passwordEncoder = $passwordEncoder;
        }

        public function supports(Request $request)
        {
            return 'app_login' === $request->attributes->get('_route')
                && $request->isMethod('POST');
        }

        public function getCredentials(Request $request)
        {
            $credentials = [
                'email' => $request->request->get('email'),
                'password' => $request->request->get('password'),
                'csrf_token' => $request->request->get('_csrf_token'),
            ];
            $request->getSession()->set(
                Security::LAST_USERNAME,
                $credentials['email']
            );

            return $credentials;
        }

        public function getUser($credentials, UserProviderInterface $userProvider)
        {
            $token = new CsrfToken('authenticate', $credentials['csrf_token']);
            if (!$this->csrfTokenManager->isTokenValid($token)) {
                throw new InvalidCsrfTokenException();
            }

            $user = $this->entityManager->getRepository(User::class)->findOneBy(['email' => $credentials['email']]);

            if (!$user) {
                // fail authentication with a custom error
                throw new CustomUserMessageAuthenticationException('Email could not be found.');
            }

            return $user;
        }

        public function checkCredentials($credentials, UserInterface $user)
        {
            return $this->passwordEncoder->isPasswordValid($user, $credentials['password']);
        }

        public function onAuthenticationSuccess(Request $request, TokenInterface $token, $providerKey)
        {
            if ($targetPath = $this->getTargetPath($request->getSession(), $providerKey)) {
                return new RedirectResponse($targetPath);
            }

            // For example : return new RedirectResponse($this->router->generate('some_route'));
            throw new \Exception('TODO: provide a valid redirect inside '.__FILE__);
        }

        protected function getLoginUrl()
        {
            return $this->router->generate('app_login');
        }
    }
    ```
    </details>

## 3. Fin de la construction du formulaire

Il est bien sûr possible de personnaliser le formulaire créé par la commande `make:auth`, son code est présent dans le template `login.html.twig`.

A la soumission du formulaire, c'est le Guard `LoginFormAuthenticator` qui intercepte la requête , lit les valeurs des champs (email et password par exemple), trouve l'objet `User`, valide le token CSRF et contrôle le password.

Il peut cependant rester quelques Todos à coder, en particulier au moins le code de la redirection en cas d'authentification avec succès :

```
// src/Security/LoginFormAuthenticator.php

// ...
public function onAuthenticationSuccess(Request $request, TokenInterface $token, $providerKey)
{
    // ...

-     throw new \Exception('TODO: provide a valid redirect inside '.__FILE__);
+     // redirect to some "app_homepage" route - of wherever you want
+     return new RedirectResponse($this->router->generate('app_homepage'));
}
```

Si connexion des users depuis la base de données, il est bien sûr nécessaire de créer des données bouchon les concernant.

La connexion est alors censée être fonctionnelle. En cas d'authentification d'un user avec succès, une partie de la wdt (avec le petit bonhomme) affiche les informations du user connecté :

![image user connected](https://symfony.com/doc/current/_images/symfony_loggedin_wdt.png)

## 4. Customisation des messages d'erreur

[doc ](https://symfony.com/doc/current/security/form_login_setup.html#controlling-error-messages)

## 5. Redirection vers la dernière page consultée

[doc ](https://symfony.com/doc/current/security/form_login_setup.html#redirecting-to-the-last-accessed-page-with-targetpathtrait)