---
url: https://planetscale.com/docs/connect/mcp
title: "Mcp"
description: ""
access_date: 2026-08-03T19:10:18.800Z
current_date: 2026-08-03T19:10:18.800Z
---

> ## Documentation Index
> Fetch the complete documentation index at: https://planetscale.com/docs/llms.txt
> Use this file to discover all available pages before exploring further.

# PlanetScale Model Context Protocol (MCP)

> Connect Claude, Cursor, Notion, and other MCP-compatible tools to your PlanetScale databases and Insights

export const PlatformAvailability = ({current, vitess, postgres}) => {
  const docsHref = path => {
    if (!path) return path;
    const normalized = path.startsWith('/') ? path : `/${path}`;
    return normalized;
  };
  const labels = {
    vitess: 'Vitess',
    postgres: 'Postgres'
  };
  if (current === 'both') {
    return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
        <span data-engine="both" data-state="current" aria-current="true" className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
          Vitess and Postgres
        </span>
      </div>;
  }
  const hasVitess = current === 'vitess' || Boolean(vitess);
  const hasPostgres = current === 'postgres' || Boolean(postgres);
  const only = !(hasVitess && hasPostgres);
  const engines = [];
  if (current === 'vitess' || current === 'postgres') engines.push(current);
  if (hasVitess && current !== 'vitess') engines.push('vitess');
  if (hasPostgres && current !== 'postgres') engines.push('postgres');
  return <div className="not-prose mb-5 flex flex-wrap items-center gap-2" role="group" aria-label="Platform availability">
      {engines.map(engine => {
    const isCurrent = current === engine;
    const href = docsHref(engine === 'vitess' ? vitess : postgres);
    const label = only ? `${labels[engine]} only` : labels[engine];
    const state = isCurrent || !href ? 'current' : 'link';
    if (isCurrent || !href) {
      return <span key={engine} data-engine={engine} data-state={state} aria-current={isCurrent ? 'true' : undefined} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
              {label}
            </span>;
    }
    return <a key={engine} href={href} data-engine={engine} data-state={state} title={`View ${labels[engine]} documentation`} className="inline-flex items-center gap-1.5 whitespace-nowrap rounded-full border px-2.5 py-1 text-[13px] font-semibold leading-tight no-underline data-[engine=vitess]:data-[state=current]:border-[#ffc59b] data-[engine=vitess]:data-[state=current]:bg-[#ffe8d8] data-[engine=vitess]:data-[state=current]:text-[#672002] dark:data-[engine=vitess]:data-[state=current]:border-[#962d00] dark:data-[engine=vitess]:data-[state=current]:bg-[#3c1403] dark:data-[engine=vitess]:data-[state=current]:text-[#ffe8d8] data-[engine=vitess]:data-[state=link]:border-[#ffc59b] data-[engine=vitess]:data-[state=link]:bg-transparent data-[engine=vitess]:data-[state=link]:text-[#b83a05] dark:data-[engine=vitess]:data-[state=link]:border-[#962d00] dark:data-[engine=vitess]:data-[state=link]:bg-transparent dark:data-[engine=vitess]:data-[state=link]:text-[#ffc59b] data-[engine=postgres]:data-[state=current]:border-[#a9dffe] data-[engine=postgres]:data-[state=current]:bg-[#ddf2ff] data-[engine=postgres]:data-[state=current]:text-[#0e3682] dark:data-[engine=postgres]:data-[state=current]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=current]:bg-[#08204e] dark:data-[engine=postgres]:data-[state=current]:text-[#ddf2ff] data-[engine=postgres]:data-[state=link]:border-[#a9dffe] data-[engine=postgres]:data-[state=link]:bg-transparent data-[engine=postgres]:data-[state=link]:text-[#0b6ec5] dark:data-[engine=postgres]:data-[state=link]:border-[#144eb6] dark:data-[engine=postgres]:data-[state=link]:bg-transparent dark:data-[engine=postgres]:data-[state=link]:text-[#73c7f9] data-[engine=both]:data-[state=current]:border-[#d4d4d4] data-[engine=both]:data-[state=current]:bg-[#f0f0f0] data-[engine=both]:data-[state=current]:text-[#3d3d3d] dark:data-[engine=both]:data-[state=current]:border-[#525252] dark:data-[engine=both]:data-[state=current]:bg-[#2a2a2a] dark:data-[engine=both]:data-[state=current]:text-[#e5e5e5]">
            {label}
            <svg aria-hidden="true" width="12" height="12" viewBox="0 0 12 12" fill="none" className="shrink-0">
              <path d="M2.5 6h7M6.5 3l3 3-3 3" stroke="currentColor" strokeWidth="1.5" strokeLinecap="round" strokeLinejoin="round" />
            </svg>
          </a>;
  })}
    </div>;
};

export const YouTubeEmbed = ({id, title}) => {
  return <Frame>
      <iframe src={`https://www.youtube-nocookie.com/embed/${id}?rel=0`} title={title} className="aspect-video w-full" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" />
    </Frame>;
};

<PlatformAvailability current="both" />

## What is the PlanetScale MCP server?

* A hosted MCP server that accesses your PlanetScale organizations, databases, branches, schema, and Insights data.
* Authenticated via OAuth for configurable access to permissions and scopes.
* Accessible from any MCP client that supports HTTP-hosted servers.

<YouTubeEmbed id="d3bc3PryOfY" title="The PlanetScale MCP server: AI for your database" />

## Quick start

<CardGroup cols={3}>
  <Card title="Cursor" icon={<svg viewBox="0 0 452 516" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M443.383 122.075L236.914 2.87153C230.284 -0.957175 222.103 -0.957175 215.473 2.87153L9.0144 122.075C3.441 125.293 0 131.244 0 137.69V378.065C0 384.501 3.441 390.462 9.0144 393.68L215.483 512.883C222.113 516.712 230.294 516.712 236.924 512.883L443.392 393.68C448.966 390.462 452.407 384.51 452.407 378.065V137.69C452.407 131.254 448.966 125.293 443.392 122.075H443.383ZM430.414 147.325L231.098 492.548C229.751 494.874 226.194 493.924 226.194 491.229V265.181C226.194 260.664 223.78 256.486 219.864 254.218L24.1063 141.199C21.78 139.852 22.7299 136.294 25.4245 136.294H424.055C429.716 136.294 433.254 142.43 430.423 147.335H430.414V147.325Z" fill="currentColor"/></svg>} href="https://cursor.com/en-US/install-mcp?name=PlanetScale&config=eyJ1cmwiOiJodHRwczovL21jcC5wc2NhbGUuZGV2L21jcC9wbGFuZXRzY2FsZSJ9">
    One-click install
  </Card>

  <Card title="VS Code" icon={<svg viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M74.9 97.4L99.1 86.1V13.7L74.9 2.5L27.6 42.9L11.4 30.9L2.1 34.5V65.4L11.4 69L27.6 57L74.9 97.4ZM74.9 27.7V72.2L40.3 50L74.9 27.7Z" fill="#007ACC"/></svg>} href="vscode:mcp/install?%7B%22url%22%3A%22https%3A%2F%2Fmcp.pscale.dev%2Fmcp%2Fplanetscale%22%2C%22name%22%3A%22PlanetScale%22%2C%22type%22%3A%22http%22%7D">
    One-click install
  </Card>

  <Card title="Claude Code" icon={<svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M4.709 15.955l4.72-2.647.08-.23-.08-.128H9.2l-.79-.048-2.698-.073-2.339-.097-2.266-.122-.571-.121L0 11.784l.055-.352.48-.321.686.06 1.52.103 2.278.158 1.652.097 2.449.255h.389l.055-.157-.134-.098-.103-.097-2.358-1.596-2.552-1.688-1.336-.972-.724-.491-.364-.462-.158-1.008.656-.722.881.06.225.061.893.686 1.908 1.476 2.491 1.833.365.304.145-.103.019-.073-.164-.274-1.355-2.446-1.446-2.49-.644-1.032-.17-.619a2.97 2.97 0 01-.104-.729L6.283.134 6.696 0l.996.134.42.364.62 1.414 1.002 2.229 1.555 3.03.456.898.243.832.091.255h.158V9.01l.128-1.706.237-2.095.23-2.695.08-.76.376-.91.747-.492.584.28.48.685-.067.444-.286 1.851-.559 2.903-.364 1.942h.212l.243-.242.985-1.306 1.652-2.064.73-.82.85-.904.547-.431h1.033l.76 1.129-.34 1.166-1.064 1.347-.881 1.142-1.264 1.7-.79 1.36.073.11.188-.02 2.856-.606 1.543-.28 1.841-.315.833.388.091.395-.328.807-1.969.486-2.309.462-3.439.813-.042.03.049.061 1.549.146.662.036h1.622l3.02.225.79.522.474.638-.079.485-1.215.62-1.64-.389-3.829-.91-1.312-.329h-.182v.11l1.093 1.068 2.006 1.81 2.509 2.33.127.578-.322.455-.34-.049-2.205-1.657-.851-.747-1.926-1.62h-.128v.17l.444.649 2.345 3.521.122 1.08-.17.353-.608.213-.668-.122-1.374-1.925-1.415-2.167-1.143-1.943-.14.08-.674 7.254-.316.37-.729.28-.607-.461-.322-.747.322-1.476.389-1.924.315-1.53.286-1.9.17-.632-.012-.042-.14.018-1.434 1.967-2.18 2.945-1.726 1.845-.414.164-.717-.37.067-.662.401-.589 2.388-3.036 1.44-1.882.93-1.086-.006-.158h-.055L4.132 18.56l-1.13.146-.487-.456.061-.746.231-.243 1.908-1.312-.006.006z" fill="#D97757"/></svg>} href="#claude-code">
    See instructions
  </Card>

  <Card title="OpenCode" icon={<svg viewBox="0 0 24 36" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M18 30H6V18H18V30Z" fill="#CFCECD"/><path d="M18 12H6V30H18V12ZM24 36H0V6H24V36Z" fill="#656363"/></svg>} href="#opencode">
    See instructions
  </Card>

  <Card title="Codex" icon={<svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M22.282 9.821a5.985 5.985 0 0 0-.516-4.91 6.046 6.046 0 0 0-6.51-2.9A6.065 6.065 0 0 0 4.981 4.18a5.985 5.985 0 0 0-3.998 2.9 6.046 6.046 0 0 0 .743 7.097 5.98 5.98 0 0 0 .51 4.911 6.051 6.051 0 0 0 6.515 2.9A5.985 5.985 0 0 0 13.26 24a6.056 6.056 0 0 0 5.772-4.206 5.99 5.99 0 0 0 3.997-2.9 6.056 6.056 0 0 0-.747-7.073zM13.26 22.43a4.476 4.476 0 0 1-2.876-1.04l.141-.081 4.779-2.758a.795.795 0 0 0 .392-.681v-6.737l2.02 1.168a.071.071 0 0 1 .038.052v5.583a4.504 4.504 0 0 1-4.494 4.494zM3.6 18.304a4.47 4.47 0 0 1-.535-3.014l.142.085 4.783 2.759a.771.771 0 0 0 .78 0l5.843-3.369v2.332a.08.08 0 0 1-.033.062L9.74 19.95a4.5 4.5 0 0 1-6.14-1.646zM2.34 7.896a4.485 4.485 0 0 1 2.366-1.973V11.6a.766.766 0 0 0 .388.676l5.815 3.355-2.02 1.168a.076.076 0 0 1-.071 0l-4.83-2.786A4.504 4.504 0 0 1 2.34 7.872zm16.597 3.855l-5.833-3.387L15.119 7.2a.076.076 0 0 1 .071 0l4.83 2.791a4.494 4.494 0 0 1-.676 8.105v-5.678a.79.79 0 0 0-.407-.667zm2.01-3.023l-.141-.085-4.774-2.782a.776.776 0 0 0-.785 0L9.409 9.23V6.897a.066.066 0 0 1 .028-.061l4.83-2.787a4.5 4.5 0 0 1 6.68 4.66zm-12.64 4.135l-2.02-1.164a.08.08 0 0 1-.038-.057V6.075a4.5 4.5 0 0 1 7.375-3.453l-.142.08L8.704 5.46a.795.795 0 0 0-.393.681zm1.097-2.365l2.602-1.5 2.607 1.5v2.999l-2.597 1.5-2.607-1.5z" fill="currentColor"/></svg>} href="#codex-cli">
    See instructions
  </Card>

  <Card title="Notion" icon={<svg viewBox="0 0 33 34" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M3.8051 3.26755L20.5301 2.04319C22.5839 1.86808 23.1124 1.98538 24.4032 2.91756L29.7421 6.64773C30.623 7.28917 30.9165 7.46381 30.9165 8.16307V28.6217C30.9165 29.9038 30.4468 30.6622 28.804 30.7782L9.38138 31.9442C8.14825 32.0027 7.56135 31.8279 6.91556 31.0114L2.98395 25.9405C2.27947 25.0072 1.98651 24.3088 1.98651 23.4918V5.3068C1.98651 4.25826 2.45649 3.38366 3.8051 3.26755Z" fill="#fff"/><path fillRule="evenodd" clipRule="evenodd" d="M3.64643 1.29903L20.3723 0.0746037C21.3849 -0.0114809 22.3097 -0.0595444 23.1918 0.139197C24.141 0.353054 24.86 0.807308 25.5578 1.31054L30.9002 5.04319L30.9158 5.05461C30.9547 5.08281 30.9968 5.11312 31.0417 5.14536C31.3674 5.37943 31.8354 5.71564 32.1631 6.09295C32.7252 6.73997 32.9031 7.45237 32.9031 8.16303V28.6217C32.9031 29.4467 32.763 30.5442 31.967 31.4425C31.1549 32.3592 30.0175 32.6721 28.9448 32.7479L28.9343 32.7486L9.48857 33.916L9.47602 33.9165C8.79263 33.949 8.01197 33.9383 7.24718 33.6609C6.41395 33.3586 5.82508 32.8277 5.35391 32.2318L5.34799 32.2243L1.40271 27.1359L1.39499 27.1257C0.55231 26.0092 0 24.8994 0 23.4918V5.30675C0 4.51862 0.17342 3.55089 0.82429 2.72219C1.51537 1.84231 2.52546 1.39554 3.6337 1.30013L3.64643 1.29903ZM20.5301 2.04315L3.80509 3.26752C2.45647 3.38361 1.9865 4.25823 1.9865 5.30675V23.4918C1.9865 24.3088 2.27946 25.0072 2.98394 25.9405L6.91553 31.0114C7.56133 31.8279 8.14822 32.0025 9.38137 31.944L28.804 30.7782C30.4468 30.6622 30.9165 29.9039 30.9165 28.6217V8.16303C30.9165 7.50025 30.6529 7.30878 29.8751 6.74438C29.8323 6.71333 29.788 6.68115 29.7421 6.6477L24.4032 2.91752C23.1124 1.98534 22.5839 1.86805 20.5301 2.04315Z" fill="#fff"/><path d="M20.5301 2.04318C22.5838 1.86808 23.1124 1.98541 24.4031 2.91757L29.7421 6.64778C30.623 7.28918 30.9167 7.46383 30.9167 8.16301V28.6217C30.9167 29.9039 30.4468 30.6622 28.804 30.7782L9.38127 31.944C8.14822 32.0025 7.56137 31.8279 6.9156 31.0114L2.98396 25.9405C2.27951 25.0072 1.98647 24.3088 1.98645 23.492V5.30687C1.98645 4.25835 2.45646 3.38365 3.80508 3.26754L20.5301 2.04318ZM28.9214 9.91165C28.9214 9.15462 28.6285 8.74625 27.9818 8.80449L8.91064 9.91165C8.20688 9.97045 7.9722 10.3204 7.9722 11.0779V28.4466C7.97222 29.3801 8.44147 29.7293 9.49759 29.6715L27.7471 28.6217C28.8037 28.5641 28.9214 27.922 28.9214 27.1636V9.91165ZM25.988 12.0096C26.1051 12.5347 25.988 13.0592 25.4588 13.1182L24.5795 13.2926V26.1151C23.816 26.5231 23.1122 26.7563 22.5256 26.7563C21.5863 26.7563 21.351 26.4646 20.6475 25.5908L14.8959 16.6149V25.2992L16.7158 25.7076C16.7158 25.7076 16.7159 26.7563 15.2475 26.7563L11.1994 26.9897C11.0818 26.7563 11.1995 26.1739 11.6101 26.0571L12.6664 25.7662V14.2837L11.1997 14.1668C11.0822 13.6417 11.3751 12.8847 12.1972 12.8259L16.5398 12.5349L22.5256 21.6277V13.5839L20.9993 13.4098C20.8821 12.7679 21.351 12.3018 21.9379 12.244L25.988 12.0096ZM23.816 4.43331C23.2877 4.02552 22.5835 3.55846 21.2343 3.67528L5.15507 4.84121C4.56875 4.89903 4.45158 5.19046 4.68509 5.42409L6.97519 7.23083C7.91323 7.98837 8.26511 7.93069 10.0265 7.81388L26.632 6.82259C26.9842 6.82259 26.6915 6.47348 26.5739 6.41536L23.816 4.43331Z" fill="currentColor"/></svg>} href="#notion">
    See instructions
  </Card>

  <Card title="Other clients" icon="terminal" href="#installation-instructions">
    See all options
  </Card>
</CardGroup>

## Authentication

The hosted MCP server uses **OAuth** so you can authorize access to PlanetScale organizations, databases, branches, and Insights data directly in your client via the MCP server.

* Each client (for example, Claude Code, Cursor, or other MCP-compatible tools) registers as an OAuth application with PlanetScale.
* When you connect the MCP server from your client, you're redirected to PlanetScale to sign in and grant access.

**Server URL:**

```
https://mcp.pscale.dev/mcp/planetscale
```

You can also use a [PlanetScale service token](../api/service-tokens.md) to authenticate requests to the MCP. Export `PLANETSCALE_API_TOKEN` environment variable as your service token to bypass browser OAuth. This is particularly useful for headless / CI environments.

<Tip>
  If you only need access to Insights data and Schema Recommendations (no query execution), use the insights-only server: `https://mcp.pscale.dev/mcp/planetscale-insights-only`. This server excludes the `planetscale_execute_read_query` and `planetscale_execute_write_query` tools.
</Tip>

## Security and credentials

* Each query uses short-lived, ephemeral credentials that are created on demand and deleted immediately after execution.

<Warning>
  We advise caution when giving LLMs *write* access to any production database. Always carefully review queries before execution.
</Warning>

### Scopes and permissions

Permissions are controlled through OAuth scopes:

* Scopes define which organizations, databases, branches, or features the MCP server can see.
* You choose whether the MCP server has no access, read-only access, or full access to databases at the organization or per-database level.

Most MCP clients provide a way to re-authenticate the MCP server if you need to update your permissions.

## Installation instructions

### Cursor

<Card title="Cursor" icon={<svg viewBox="0 0 452 516" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M443.383 122.075L236.914 2.87153C230.284 -0.957175 222.103 -0.957175 215.473 2.87153L9.0144 122.075C3.441 125.293 0 131.244 0 137.69V378.065C0 384.501 3.441 390.462 9.0144 393.68L215.483 512.883C222.113 516.712 230.294 516.712 236.924 512.883L443.392 393.68C448.966 390.462 452.407 384.51 452.407 378.065V137.69C452.407 131.254 448.966 125.293 443.392 122.075H443.383ZM430.414 147.325L231.098 492.548C229.751 494.874 226.194 493.924 226.194 491.229V265.181C226.194 260.664 223.78 256.486 219.864 254.218L24.1063 141.199C21.78 139.852 22.7299 136.294 25.4245 136.294H424.055C429.716 136.294 433.254 142.43 430.423 147.335H430.414V147.325Z" fill="currentColor"/></svg>} horizontal href="https://cursor.com/en-US/install-mcp?name=PlanetScale&config=eyJ1cmwiOiJodHRwczovL21jcC5wc2NhbGUuZGV2L21jcC9wbGFuZXRzY2FsZSJ9">
  Click to install the MCP server configuration for Cursor.
</Card>

Manual installation:

1. Open the command palette and type "Cursor Settings"
2. Under "Tools & MCP" click "New MCP Server"
3. Paste the following JSON into the configuration file that opens

```json .cursor/mcp.json theme={null}
{
  "mcpServers": {
    "planetscale": {
      "url": "https://mcp.pscale.dev/mcp/planetscale"
    }
  }
}
```

4. Once this configuration is saved, Cursor will attempt to authenticate and show a login prompt. Select this prompt to grant Cursor access to your PlanetScale account.
5. You may need to restart Cursor to load the new configuration.

### VS Code

<Card title="VS Code" icon={<svg viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M74.9 97.4L99.1 86.1V13.7L74.9 2.5L27.6 42.9L11.4 30.9L2.1 34.5V65.4L11.4 69L27.6 57L74.9 97.4ZM74.9 27.7V72.2L40.3 50L74.9 27.7Z" fill="#007ACC"/></svg>} horizontal href="vscode:mcp/install?%7B%22name%22%3A%22PlanetScale%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.pscale.dev%2Fmcp%2Fplanetscale%22%7D">
  Click to install the MCP server configuration for VS Code.
</Card>

Use the button above to launch VS Code and configure the PlanetScale MCP server automatically. Alternatively, follow the steps below to do it manually:

1. Open the Command Palette (`Ctrl+Shift+P` on Windows/Linux or `Cmd+Shift+P` on macOS)
2. Type "MCP: Add Server"
3. Choose "HTTP"
4. Enter the following details:
   * URL: [https://mcp.pscale.dev/mcp/planetscale](https://mcp.pscale.dev/mcp/planetscale)
   * Name: "PlanetScale"
5. Click "Add"

**Authorization**

After adding the PlanetScale MCP server you will need to start the server and authorize:

1. Open the Command Palette (`Ctrl+Shift+P` on Windows/Linux or `Cmd+Shift+P` on macOS)
2. Type "MCP: List Servers"
3. Select "PlanetScale"
4. Click "Start Server"
5. When the dialog appears saying "The MCP Server Definition 'PlanetScale' wants to authenticate to PlanetScale MCP," click "Allow"
6. A popup will ask "Do you want Code to open the external website?" — click "Cancel"
7. You'll see a message: "Having trouble authenticating to 'PlanetScale MCP'? Would you like to try a different way? (URL Handler)"
8. Click "Yes"
9. Click "Open" and complete the PlanetScale sign-in flow to connect to PlanetScale MCP

### OpenCode

OpenCode supports automatic authentication for OAuth-based remote MCP servers.

1. Ensure [OpenCode](https://opencode.ai/) is installed and available in your terminal

```bash theme={null}
opencode --version
```

2. Add the PlanetScale MCP server to your OpenCode configuration file.

```json ~/.config/opencode/opencode.json theme={null}
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "planetscale": {
      "type": "remote",
      "url": "https://mcp.pscale.dev/mcp/planetscale"
    }
  }
}
```

OpenCode configuration files can be stored inside a project or globally, see the [OpenCode configuration file](https://opencode.ai/docs/config/#precedence-order) documentation for more information.

3. Enter the following to authenticate with the PlanetScale MCP server

```bash theme={null}
opencode mcp auth planetscale
```

4. Enter OpenCode

```bash theme={null}
opencode
```

5. List the available MCP servers with the `/mcp` slash command.

```bash theme={null}
/mcp
```

You should see the PlanetScale MCP server listed and connected.

```bash theme={null}
planetscale connected
```

### Claude.ai and Claude for desktop

The PlanetScale MCP server is available as a connector in the [Claude Connectors Directory](https://claude.com/connectors/planetscale).

1. Navigate to **Settings > Connectors**
2. Click **Browse connectors**
3. Search for "PlanetScale"
4. Click **+**
5. Follow the prompts to authenticate with your PlanetScale account

Alternatively, install it as a custom connector:

1. Click **Add custom connector** at the bottom of the section
2. Enter the PlanetScale MCP server URL: `https://mcp.pscale.dev/mcp/planetscale`
3. Click **Add** to save the connector
4. Follow the prompts to authenticate with your PlanetScale account

<Note>
  Custom connectors using remote MCP are not available on all plans. See the [Claude Support page](https://support.claude.com/en/articles/11175166-getting-started-with-custom-connectors-using-remote-mcp) for more information.
</Note>

To use the connector in a conversation:

1. Click the **+** button in the lower left of your chat interface
2. Select **Connectors**
3. Toggle the PlanetScale connector on for that conversation

### Claude Code

The PlanetScale MCP server is available as a [Claude Code plugin](https://github.com/planetscale/claude-plugin). You must first add the PlanetScale marketplace, and then install the plugin.

```
/plugin marketplace add planetscale/claude-plugin
/plugin install planetscale@planetscale
```

Alternatively, you can add the MCP server directly to your Claude Code configuration:

1. Ensure [Claude Code](https://code.claude.com/docs/en/overview) is available in your terminal

```bash theme={null}
claude --version
```

2. Run the following command to add the PlanetScale MCP server to your Claude Code

```bash theme={null}
claude mcp add --transport http "planetscale" https://mcp.pscale.dev/mcp/planetscale
```

3. Enter Claude Code

```bash theme={null}
claude
```

4. List the available MCP servers with the `/mcp` slash command.

```bash theme={null}
/mcp
```

You should see the PlanetScale MCP server listed in need of authentication. Select it, press "Enter" and follow the instructions in the browser to authenticate.

```bash theme={null}
❯ planetscale · △ needs authentication
```

You should see the following message once authenticated:

```bash theme={null}
❯ /mcp
  ⎿  Authentication successful. Connected to planetscale.
```

### Claude Managed Agents

Connect the PlanetScale MCP server to [Claude Managed Agents](https://platform.claude.com) using a credential vault.

1. Go to [platform.claude.com](https://platform.claude.com)
2. Open **Credential vaults** → **Create vault** → **Add credential**
3. Select **MCP OAuth**
4. Enter the server URL: `https://mcp.pscale.dev/mcp/planetscale`
5. Leave **Access token** and **OAuth client credentials** blank
6. Click **Connect**
7. Complete the PlanetScale sign-in flow when prompted

### Gemini CLI

1. Ensure [Gemini CLI](https://geminicli.com/) is available in your terminal

```bash theme={null}
gemini --version
```

2. Run the following command to add the PlanetScale MCP server to your Gemini CLI

```bash theme={null}
gemini mcp add --transport http "planetscale" https://mcp.pscale.dev/mcp/planetscale
```

3. Enter Gemini CLI

```bash theme={null}
gemini
```

4. List the available MCP servers with the `/mcp` slash command and select "List"

```bash theme={null}
/mcp list
```

You should see the PlanetScale MCP server listed in need of authentication.

```bash theme={null}
🔴 planetscale - Disconnected (OAuth not authenticated)
```

Now run the following command to authenticate:

```bash theme={null}
/mcp auth planetscale
```

You should see the following message once authenticated:

```bash theme={null}
ℹ ✅ Successfully authenticated with MCP server 'planetscale'!
```

### Codex CLI

1. Ensure [Codex CLI](https://developers.openai.com/codex/cli/) is available in your terminal

```bash theme={null}
codex --version
```

2. Run the following command to add the PlanetScale MCP server to your Codex CLI

```bash theme={null}
codex mcp add planetscale --url https://mcp.pscale.dev/mcp/planetscale
```

You should be immediately prompted to authenticate. Follow the instructions in the browser to authenticate.

3. Enter Codex CLI

```bash theme={null}
codex
```

4. List the available MCP servers with the `/mcp` slash command

```bash theme={null}
/mcp
```

You should see the PlanetScale MCP server listed, enabled and authenticated.

```bash theme={null}
• planetscale
  • Status: enabled
  • Auth: OAuth
```

### Amp CLI

1. Ensure [Amp CLI](https://ampcode.com/) is available in your terminal

```bash theme={null}
amp --version
```

2. Run the following command to add the PlanetScale MCP server to your Amp CLI

```bash theme={null}
amp mcp add planetscale https://mcp.pscale.dev/mcp/planetscale
```

3. Enter Amp CLI

```bash theme={null}
amp
```

4. You should be immediately prompted to authenticate. Follow the instructions in the browser.

5. List the available MCP servers with the `/mcp list tools` command.

```bash theme={null}
/mcp list tools
```

You should see the PlanetScale MCP server listed and its available tools.

### Notion

You can access the PlanetScale MCP server from inside a Notion Custom Agent.

1. Create a new Notion Agent or open an existing one
2. In the Custom Agent open "Settings" and scroll to "Tools and access"
3. Click "Add connection"
4. Insert the MCP server URL: `https://mcp.pscale.dev/mcp/planetscale`
5. Ensure Authentication is set to OAuth
6. Click "Connect"
7. Complete the authentication process to give the Notion Custom Agent access to specific organizations and databases

## Help your agent target your project's database

By default, the MCP server is likely to scan through all your organizations and databases to find those relevant to your query. If you are working within a project that targets a single database, it's a good idea to include this information in your `AGENTS.md` file to skip these tool calls.

For example:

```markdown AGENTS.md theme={null}
## PlanetScale Database

- **Organization:** `my-organization`
- **Database:** `my-database`
- **Branch:** `main`
```

## Query handling

All queries execute over HTTP via the hosted MCP server. This makes the MCP server useful in environments that normally cannot run database queries directly, such as browser-based tools or sandboxed AI agents.

The PlanetScale MCP server includes several built-in behaviors to help ensure safe, observable, and performant query execution.

### Read queries

* Read queries route to a replica when your branch has replicas configured, reducing load on the primary. This is the default behavior.
* Set `use_replica` to `false` on `planetscale_execute_read_query` to run against the primary instead. Works for both Vitess and Postgres databases.
* All queries include a `source=planetscale-mcp` comment, making them easy to identify and track in [Insights](../what-is-planetscale.md#insights).

#### Postgres row-level security

For Postgres databases, read queries run with a short-lived role that has `pg_read_all_data`. This role does not bypass row-level security (RLS).

When a read query returns zero rows or a zero count (such as `SELECT COUNT(*)` returning 0), the MCP server checks whether RLS is active on tables you can access. If so, the response includes a `warnings` array with code `postgres_rls_active`, listing the affected relations. A zero-row result does not necessarily mean the table is empty — RLS may be hiding rows from the MCP role.

### Write query safeguards

To help prevent accidental data loss, the MCP server blocks certain operations:

* `UPDATE` or `DELETE` statements without a `WHERE` clause are blocked.
* `TRUNCATE` statements are blocked.
* DDL statements (`CREATE`, `DROP`, `ALTER`, etc.) prompt the LLM to request human confirmation before proceeding.

### Postgres database targeting

For PlanetScale Postgres databases, the MCP server connects to the default `postgres` database by default. If you have created additional databases in the same cluster using `CREATE DATABASE`, you can specify which database to query using the `postgres_database_name` parameter.

This parameter is available on both the `planetscale_execute_read_query` and `planetscale_execute_write_query` tools. When omitted, queries run against the default `postgres` database for your branch.

## Example workflows

Once installed, you can ask your MCP-enabled editor or agent to:

* "Show me all databases in my PlanetScale organization and highlight anything running on PlanetScale Metal."
* "List the branches for my production database and summarize their differences."
* "Look at my slowest queries over the last day and suggest index or query changes."
* "Check whether the CPU and memory profile for my database is appropriate for the current workload."
* "Explain what changed between yesterday's and today's query patterns in Insights."

You can also run these workflows on a recurring schedule using [Cursor Automations](https://cursor.com/docs/cloud-agent/automations). See [Self-improving database](self-improving-database.md) for ready-to-use prompts.

## Available tools

The hosted PlanetScale MCP server includes a curated set of tools designed for day-to-day database exploration, debugging, and insights.

The available tools are:

1. `planetscale_list_organizations` - List all PlanetScale organizations you have access to.
2. `planetscale_list_databases` - List all databases within an organization.
3. `planetscale_list_branches` - List all branches within a database.
4. `planetscale_get_organization` - Get details about a specific organization.
5. `planetscale_get_database` - Get details about a specific database.
6. `planetscale_get_branch` - Get details about a database branch.
7. `planetscale_get_branch_schema` - Get the schema for a database branch.
8. `planetscale_execute_read_query` - Execute a read-only SQL query (SELECT, SHOW, DESCRIBE, EXPLAIN) against a PlanetScale database. Optionally accepts `use_replica` to route to a replica (default) or primary. For Postgres, optionally accepts `postgres_database_name` to target a non-default database. Returns RLS warnings when policies may filter results.
9. `planetscale_execute_write_query` - Execute a write SQL query (INSERT, UPDATE, DELETE, or DDL) against a PlanetScale database. For Postgres, optionally accepts `postgres_database_name` to target a non-default database.
10. `planetscale_get_insights` - Get query performance insights for a PlanetScale database branch.
11. `planetscale_list_regions_for_organization` - List all available regions for an organization.
12. `planetscale_list_cluster_size_skus` - List all available cluster size SKUs.
13. `planetscale_list_invoices` - List all invoices for an organization.
14. `planetscale_get_invoice_line_items` - Get all line items for an invoice, with prorated costs broken down by database branch.
15. `planetscale_search_documentation` - Search the PlanetScale documentation.
16. `planetscale_list_schema_recommendations` - List schema recommendations for a database, including suggestions for adding indexes, removing redundant indexes, and preventing primary key exhaustion.

The MCP server tools are [open source and available on GitHub](https://github.com/planetscale/mcp-server).

## Troubleshooting

If your MCP client cannot connect or tools fail to run:

1. **Restart your client or CLI tool**

   After installation, you may need to restart your client or CLI tool to load the new MCP configuration.

2. **Check the server URL**

   Make sure the MCP server URL is set to the hosted endpoint (for example, `https://mcp.pscale.dev/mcp/planetscale`).

3. **Re-authorize the MCP server**

   If scopes or tokens have changed, reauthorize the PlanetScale MCP server in your client so it can request fresh tokens.

4. **Verify organization and database access**

   Confirm that your PlanetScale user account has access to the orgs and databases you expect to see.

## PlanetScale CLI MCP server

The local-only [PlanetScale CLI](../cli/mcp.md) MCP server has been deprecated and is no longer recommended.

## Need help?

Get help from [the PlanetScale Support team](https://planetscale.com/contact?initial=support), or join our [Discord community](https://pscale.link/community) to see how others are using PlanetScale.
