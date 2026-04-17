# Contribuer à PROJECT M&M

Salut ! Voici les conventions qu'on suit sur l'org.

## Workflow de base

1. **Crée une branche** depuis `main` avec un nom explicite
   - `feat/nom-feature`
   - `fix/nom-bug`
   - `docs/nom-doc`
   - `chore/nom-task`
2. **Commit** avec des messages clairs (voir convention ci-dessous)
3. **Push** et ouvre une Pull Request
4. **Demande une review** à l'autre dev
5. **Merge** une fois approuvé (et CI green)

## Convention de commit

On suit [Conventional Commits](https://www.conventionalcommits.org) de manière souple :

```
type(scope): courte description

[corps optionnel]
```

Types courants : `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `ci`, `perf`.

Exemples :
- `feat(auth): ajoute login via Google`
- `fix(api): corrige timeout sur endpoint /users`
- `docs: met à jour le README`

## Code style

- Chaque projet a son linter configuré dans la CI. Respecte ce que dit le linter.
- Pas de code commenté laissé derrière — supprime.
- Noms explicites > commentaires.

## Tests

- Ajoute des tests pour le code que tu ajoutes, dans la mesure du possible.
- Si tu fix un bug, ajoute un test qui reproduit le bug.

## Reviews

- Soyez directs et constructifs.
- Pas de "LGTM" aveugle — lis le code.
- Les petites PR sont plus faciles à reviewer que les mastodontes.

## Secrets

- **Jamais** de secret (API key, token, mot de passe) dans le code ou dans un commit.
- Utilise des variables d'environnement + `.env.local` (ignoré dans `.gitignore`).
- Pour les secrets CI, utilise les [secrets GitHub Actions](https://docs.github.com/actions/security-guides/encrypted-secrets).

## Questions ?

Ouvre une issue ou démarre une discussion.
