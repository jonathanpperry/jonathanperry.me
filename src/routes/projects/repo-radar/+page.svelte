<svelte:head>
  <title>Repo Radar | Jonathan Perry</title>
  <meta
    name="description"
    content="How I built Repo Radar, an Electron desktop application for discovering local Git repositories, inspecting their status, and opening projects in VS Code."
  />
</svelte:head>

<main class="mx-auto max-w-5xl px-6 py-12 sm:py-20">
  <a
    href="/#projects"
    class="mb-10 inline-block text-violet-400 hover:underline"
  >
    ← Back to Projects
  </a>

  <article class="space-y-16">
    <header class="space-y-6">
      <p class="text-sm font-medium uppercase tracking-widest text-violet-400">
        Desktop Application · Case Study
      </p>

      <h1 class="text-4xl font-bold sm:text-5xl">Repo Radar</h1>

      <p class="max-w-3xl text-xl leading-relaxed text-slate-300">
        A clearer view of my local development projects, without opening every
        folder and checking Git individually.
      </p>

      <ul
        aria-label="Technology stack"
        class="flex flex-wrap gap-2 text-sm text-violet-200"
      >
        {#each ["Electron", "React", "TypeScript", "Node.js", "Vitest"] as technology}
          <li class="rounded-full border border-violet-400/30 px-3 py-1">
            {technology}
          </li>
        {/each}
      </ul>

      <a
        href="https://github.com/jonathanpperry/repo-radar"
        target="_blank"
        rel="noopener noreferrer"
        class="inline-flex rounded-lg bg-violet-400 px-5 py-3 font-semibold
          text-slate-950 transition hover:bg-violet-300
          focus-visible:outline focus-visible:outline-2
          focus-visible:outline-offset-4 focus-visible:outline-violet-400"
      >
        View source on GitHub ↗
      </a>

      <p class="text-sm text-slate-400">
        Personal project · Actively developed
      </p>
    </header>

    <!-- Add a current application screenshot here when one is available. -->
    <figure class="space-y-3">
      <a
        href="/images/repo-radar-snapshot.png"
        target="_blank"
        rel="noopener noreferrer"
        aria-label="View full-size Repo Radar screenshot"
      >
        <img
          src="/images/repo-radar-snapshot.png"
          alt="Repo Radar showing local repositories with their branches, Git status, and last commit dates"
          class="h-auto w-full rounded-xl border border-slate-700 shadow-xl"
        />
      </a>

      <figcaption class="text-center text-sm text-slate-400">
        Local repositories, Git status, and recent activity in one place. Click
        to view full size.
      </figcaption>
    </figure>

    <section aria-labelledby="problem-heading" class="space-y-4">
      <h2 id="problem-heading" class="text-2xl font-semibold">The problem</h2>

      <p class="max-w-3xl leading-relaxed text-slate-300">
        With development projects spread across local folders, finding where I
        left off requires repeated context switching. Which repository has
        uncommitted changes? Which one did I work on recently? Where is the
        project I want to open?
      </p>

      <p class="max-w-3xl leading-relaxed text-slate-300">
        I built Repo Radar to bring those answers into one desktop interface. It
        inspects local repositories, so a project does not need to be hosted on
        GitHub to appear.
      </p>
    </section>

    <section aria-labelledby="workflow-heading" class="space-y-6">
      <h2 id="workflow-heading" class="text-2xl font-semibold">
        From folder to working project
      </h2>

      <ol class="grid gap-4 md:grid-cols-3">
        <li class="rounded-xl border border-slate-700 bg-slate-900/50 p-6">
          <p class="mb-3 text-sm font-semibold text-violet-400">01</p>
          <h3 class="text-lg font-semibold">Choose a folder</h3>
          <p class="mt-2 leading-relaxed text-slate-300">
            Select a projects directory using the native folder picker. Repo
            Radar searches its nested folders for Git repositories.
          </p>
        </li>

        <li class="rounded-xl border border-slate-700 bg-slate-900/50 p-6">
          <p class="mb-3 text-sm font-semibold text-violet-400">02</p>
          <h3 class="text-lg font-semibold">See where things stand</h3>
          <p class="mt-2 leading-relaxed text-slate-300">
            Review branches, uncommitted changes, and last-commit dates.
            Repositories with the newest commits appear first.
          </p>
        </li>

        <li class="rounded-xl border border-slate-700 bg-slate-900/50 p-6">
          <p class="mb-3 text-sm font-semibold text-violet-400">03</p>
          <h3 class="text-lg font-semibold">Get back to work</h3>
          <p class="mt-2 leading-relaxed text-slate-300">
            Open a repository in VS Code directly from its card, with loading
            feedback while the launch request is in progress.
          </p>
        </li>
      </ol>
    </section>

    <section aria-labelledby="architecture-heading" class="space-y-6">
      <div class="space-y-3">
        <h2 id="architecture-heading" class="text-2xl font-semibold">
          Architecture: separating UI from system access
        </h2>

        <p class="max-w-3xl leading-relaxed text-slate-300">
          Electron provides the desktop integration, while React handles the
          interface. A preload bridge connects the two through specific
          operations and shared TypeScript types.
        </p>
      </div>

      <dl class="divide-y divide-slate-700 rounded-xl border border-slate-700">
        <div class="grid gap-2 p-6 sm:grid-cols-3 sm:gap-6">
          <dt class="font-semibold text-violet-300">React renderer</dt>
          <dd class="leading-relaxed text-slate-300 sm:col-span-2">
            Displays repository cards and manages scan loading, errors, and
            per-repository launch state.
          </dd>
        </div>

        <div class="grid gap-2 p-6 sm:grid-cols-3 sm:gap-6">
          <dt class="font-semibold text-violet-300">Preload bridge</dt>
          <dd class="leading-relaxed text-slate-300 sm:col-span-2">
            Exposes methods for choosing and scanning a folder and opening a
            repository in VS Code through inter-process communication.
          </dd>
        </div>

        <div class="grid gap-2 p-6 sm:grid-cols-3 sm:gap-6">
          <dt class="font-semibold text-violet-300">Electron main process</dt>
          <dd class="leading-relaxed text-slate-300 sm:col-span-2">
            Owns native dialogs, filesystem traversal, Git subprocesses, and
            editor launching. Scanning and Git inspection live in separate
            modules.
          </dd>
        </div>
      </dl>

      <p class="max-w-3xl leading-relaxed text-slate-300">
        This separation lets me test repository discovery without launching the
        desktop interface. It also introduces an explicit boundary where
        requests, results, and failures need careful handling.
      </p>
    </section>

    <section aria-labelledby="decisions-heading" class="space-y-6">
      <h2 id="decisions-heading" class="text-2xl font-semibold">
        Engineering decisions and tradeoffs
      </h2>

      <div class="space-y-8">
        <div class="space-y-2">
          <h3 class="text-lg font-semibold">Keep discovery focused</h3>
          <p class="leading-relaxed text-slate-300">
            The scanner skips dependency and generated directories such as
            <code class="text-violet-200">node_modules</code>,
            <code class="text-violet-200">dist</code>,
            <code class="text-violet-200">build</code>, and
            <code class="text-violet-200">out</code>. It also stops descending
            once it finds a repository. This avoids unnecessary traversal, but
            intentionally omits nested repositories and projects inside those
            excluded directories.
          </p>
        </div>

        <div class="space-y-2">
          <h3 class="text-lg font-semibold">Preserve useful partial results</h3>
          <p class="leading-relaxed text-slate-300">
            An unreadable selected folder fails the scan. An unreadable child
            folder produces a warning while discovery continues elsewhere. If
            Git status cannot be retrieved, the repository can still appear with
            an explanation.
          </p>
        </div>

        <div class="space-y-2">
          <h3 class="text-lg font-semibold">Use predictable Git inspection</h3>
          <p class="leading-relaxed text-slate-300">
            Git commands run with separate executable arguments, a timeout, and
            an output limit. Branch, status, and last-commit queries run
            concurrently within each repository, while directory traversal
            remains sequential.
          </p>
        </div>

        <div class="space-y-2">
          <h3 class="text-lg font-semibold">Make recent work easy to find</h3>
          <p class="leading-relaxed text-slate-300">
            Results are ordered by last-commit date, with repositories lacking a
            date placed afterward. When neither repository has a date, sorting
            falls back to its name. Commit recency is a useful signal, though it
            does not capture every kind of local activity.
          </p>
        </div>
      </div>
    </section>

    <section aria-labelledby="testing-heading" class="space-y-6">
      <h2 id="testing-heading" class="text-2xl font-semibold">
        Testing the behavior
      </h2>

      <p class="max-w-3xl leading-relaxed text-slate-300">
        Scanner tests use real temporary directories and mocked Git status. This
        exercises filesystem traversal while keeping branch and commit data
        predictable. Temporary files are removed after each test.
      </p>

      <div class="rounded-xl border border-slate-700 bg-slate-900/50 p-6">
        <h3 class="font-semibold">Current automated coverage</h3>
        <ul class="mt-4 list-disc space-y-2 pl-5 text-slate-300">
          <li>Recursive discovery and alphabetical fallback ordering.</li>
          <li>Exclusion of common dependency and generated directories.</li>
          <li>Ordering repositories by their most recent commit date.</li>
        </ul>
      </div>

      <p class="max-w-3xl leading-relaxed text-slate-300">
        GitHub Actions currently runs linting and a production build, including
        TypeScript checks. Adding the Vitest suite to that workflow is a next
        step. The scanner tests do not yet verify actual Git subprocess behavior
        or the complete Electron interaction.
      </p>

      <a
        href="https://github.com/jonathanpperry/repo-radar/blob/main/src/main/projects.test.ts"
        target="_blank"
        rel="noopener noreferrer"
        class="inline-block text-violet-400 hover:underline"
      >
        Read the scanner tests ↗
      </a>
    </section>

    <section aria-labelledby="next-heading" class="space-y-4">
      <h2 id="next-heading" class="text-2xl font-semibold">
        What I would improve next
      </h2>

      <ul class="list-disc space-y-3 pl-5 leading-relaxed text-slate-300">
        <li>Run behavioral tests in CI alongside linting and type checking.</li>
        <li>
          Strengthen runtime request validation and the VS Code launch path.
        </li>
        <li>
          Expand coverage for scan failures, Git edge cases, and repository
          boundaries.
        </li>
        <li>
          Measure larger scans before introducing bounded concurrency, progress
          reporting, or cancellation.
        </li>
      </ul>
    </section>

    <section
      aria-labelledby="takeaway-heading"
      class="rounded-xl border border-violet-400/30 bg-violet-400/5 p-6 sm:p-8"
    >
      <h2 id="takeaway-heading" class="text-2xl font-semibold">
        What this project taught me
      </h2>

      <p class="mt-4 leading-relaxed text-slate-300">
        Even a small developer tool needs deliberate decisions about scope,
        process boundaries, and failure behavior. Repo Radar gave me a practical
        way to connect interface design with filesystem operations, subprocess
        handling, and automated tests.
      </p>

      <p class="mt-4 leading-relaxed text-slate-300">
        The most useful improvements make the application easier to trust:
        predictable discovery, understandable warnings, and tests that protect
        the behavior users depend on.
      </p>
    </section>
  </article>
</main>
