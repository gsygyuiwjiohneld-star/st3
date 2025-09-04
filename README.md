
import React, { useEffect, useMemo, useState } from "react";
import { motion } from "framer-motion";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Switch } from "@/components/ui/switch";
import { Input } from "@/components/ui/input";
import { Menu, Accessibility, Sun, Moon, Search, Highlighter, Text, LineHeight, Save, Info, HeartHandshake, Users, BarChartBig, BookOpen } from "lucide-react";

// Pequeno helper para classes condicionalmente
function cx(...classes) {
  return classes.filter(Boolean).join(" ");
}

// Persistência simples
const STORAGE_KEY = "site_acessibilidade_pref";

export default function DesenvolvimentoSocialAcessivel() {
  const [dark, setDark] = useState(false);
  const [highContrast, setHighContrast] = useState(false);
  const [dyslexia, setDyslexia] = useState(false);
  const [fontScale, setFontScale] = useState(1); // 1 = base, 1.125 = lg, 1.25 = xl
  const [lineSpace, setLineSpace] = useState(1.5); // 1.5 = relaxed
  const [announce, setAnnounce] = useState("");
  const [menuOpen, setMenuOpen] = useState(false);
  const [query, setQuery] = useState("");

  // Carrega preferências
  useEffect(() => {
    try {
      const saved = JSON.parse(localStorage.getItem(STORAGE_KEY) || "null");
      if (saved) {
        setDark(!!saved.dark);
        setHighContrast(!!saved.highContrast);
        setDyslexia(!!saved.dyslexia);
        setFontScale(saved.fontScale ?? 1);
        setLineSpace(saved.lineSpace ?? 1.5);
      }
    } catch {}
  }, []);

  // Aplica preferências visuais no <html> e salva
  useEffect(() => {
    document.documentElement.lang = "pt-BR";
    const root = document.documentElement;
    root.classList.toggle("dark", dark);
    root.classList.toggle("hc", highContrast);
    root.classList.toggle("dys", dyslexia);
    root.style.setProperty("--fs-scale", String(fontScale));
    root.style.setProperty("--lh", String(lineSpace));

    const prefs = { dark, highContrast, dyslexia, fontScale, lineSpace };
    localStorage.setItem(STORAGE_KEY, JSON.stringify(prefs));
    setAnnounce("Preferências de acessibilidade atualizadas e salvas.");
    const t = setTimeout(() => setAnnounce(""), 2000);
    return () => clearTimeout(t);
  }, [dark, highContrast, dyslexia, fontScale, lineSpace]);

  // Filtro de busca simples por textos das seções
  const sections = useMemo(() => (
    [
      {
        id: "inicio",
        icon: Info,
        title: "O que é Desenvolvimento Social",
        text: "Desenvolvimento social é o processo de melhoria contínua das condições de vida de uma população, com foco em equidade, bem‑estar, inclusão e participação cidadã.",
      },
      {
        id: "objetivos",
        icon: HeartHandshake,
        title: "Objetivos",
        text: "Reduzir desigualdades, ampliar acesso a serviços essenciais, fortalecer redes de proteção social e promover oportunidades econômicas sustentáveis.",
      },
      {
        id: "programas",
        icon: Users,
        title: "Programas e Iniciativas",
        text: "Transferência de renda, capacitação profissional, primeira infância, segurança alimentar, moradia digna e políticas de inclusão para grupos vulneráveis.",
      },
      {
        id: "indicadores",
        icon: BarChartBig,
        title: "Indicadores de Sucesso",
        text: "Taxa de pobreza, acesso à educação e saúde, segurança alimentar, empregabilidade, mobilidade social, participação comunitária e qualidade dos serviços públicos.",
      },
      {
        id: "recursos",
        icon: BookOpen,
        title: "Materiais e Recursos",
        text: "Guias práticos, bases de dados abertas, cursos gratuitos, manuais de monitoramento e avaliação, e casos de referência nacionais e internacionais.",
      },
    ]
  ), []);

  const filtered = useMemo(() => {
    const q = query.trim().toLowerCase();
    if (!q) return sections;
    return sections.filter(s => (s.title + " " + s.text).toLowerCase().includes(q));
  }, [query, sections]);

  return (
    <div className={cx("min-h-screen bg-white text-zinc-900 transition-colors", dark && "dark:bg-zinc-900 dark:text-zinc-50")}>
      {/* Estilos auxiliares inline (alto contraste, dislexia, escalas) */}
      <style>{`
        :root { font-size: calc(16px * var(--fs-scale, 1)); line-height: var(--lh, 1.5); }
        .hc * { color: #000 !important; background: #fff !important; border-color: #000 !important; }
        .hc .inverse, .dark.hc * { color: #fff !important; background: #000 !important; }
        .dys { letter-spacing: 0.02em; word-spacing: 0.05em; }
        .focus-ring:focus-visible { outline: 3px solid #2563eb; outline-offset: 3px; }
        .skip-link { position: absolute; left: -9999px; top: auto; width: 1px; height: 1px; overflow: hidden; }
        .skip-link:focus { left: 1rem; top: 1rem; width: auto; height: auto; padding: .5rem .75rem; background: #111; color: #fff; border-radius: .5rem; z-index: 50; }
      `}</style>

      {/* Link p/ pular para conteúdo */}
      <a href="#conteudo" className="skip-link">Pular para o conteúdo principal</a>

      {/* Barra superior */}
      <header className="sticky top-0 z-40 border-b bg-white/80 backdrop-blur-sm dark:bg-zinc-900/80">
        <div className="mx-auto max-w-6xl px-4 py-3 flex items-center gap-3">
          <button
            aria-label={menuOpen ? "Fechar menu" : "Abrir menu"}
            className="md:hidden p-2 rounded-2xl border focus-ring"
            onClick={() => setMenuOpen(v => !v)}
          >
            <Menu className="h-5 w-5" aria-hidden="true" />
          </button>

          <div className="flex items-center gap-2">
            <Accessibility className="h-6 w-6" aria-hidden="true" />
            <span className="text-lg font-semibold">Desenvolvimento Social</span>
          </div>

          <nav aria-label="Primária" className="ml-auto hidden md:flex items-center gap-2">
            <a className="px-3 py-2 rounded-xl hover:underline focus-ring" href="#inicio">Início</a>
            <a className="px-3 py-2 rounded-xl hover:underline focus-ring" href="#objetivos">Objetivos</a>
            <a className="px-3 py-2 rounded-xl hover:underline focus-ring" href="#programas">Programas</a>
            <a className="px-3 py-2 rounded-xl hover:underline focus-ring" href="#indicadores">Indicadores</a>
            <a className="px-3 py-2 rounded-xl hover:underline focus-ring" href="#recursos">Recursos</a>
          </nav>
        </div>

        {/* Menu mobile */}
        {menuOpen && (
          <div className="md:hidden border-t">
            <div className="mx-auto max-w-6xl px-4 py-2 flex flex-col gap-1">
              {sections.map(s => (
                <a key={s.id} className="px-3 py-2 rounded-xl hover:underline focus-ring" href={`#${s.id}`} onClick={() => setMenuOpen(false)}>
                  {s.title}
                </a>
              ))}
            </div>
          </div>
        )}
      </header>

      {/* Painel de Acessibilidade */}
      <section aria-label="Configurações de acessibilidade" className="border-b bg-zinc-50/60 dark:bg-zinc-800/60">
        <div className="mx-auto max-w-6xl px-4 py-4 grid gap-4 md:grid-cols-2 lg:grid-cols-3">
          <Card className="shadow-sm">
            <CardHeader className="pb-2">
              <CardTitle className="text-base flex items-center gap-2"><Highlighter className="h-4 w-4" /> Tema</CardTitle>
            </CardHeader>
            <CardContent className="flex items-center gap-4">
              <div className="flex items-center gap-2">
                <Sun className="h-4 w-4" aria-hidden="true" />
                <Switch id="dark" checked={dark} onCheckedChange={setDark} aria-label="Ativar modo escuro" />
                <Moon className="h-4 w-4" aria-hidden="true" />
              </div>
              <div className="flex items-center gap-2">
                <span>Alto contraste</span>
                <Switch id="hc" checked={highContrast} onCheckedChange={setHighContrast} aria-label="Ativar alto contraste" />
              </div>
            </CardContent>
          </Card>

          <Card className="shadow-sm">
            <CardHeader className="pb-2">
              <CardTitle className="text-base flex items-center gap-2"><Text className="h-4 w-4" /> Texto</CardTitle>
            </CardHeader>
            <CardContent className="flex flex-wrap items-center gap-3">
              <div className="flex items-center gap-2" role="group" aria-label="Tamanho da fonte">
                <Button variant="secondary" className="rounded-2xl" onClick={() => setFontScale(v => Math.max(0.875, +(v - 0.125).toFixed(3)))} aria-label="Diminuir fonte">A‑</Button>
                <Button variant="secondary" className="rounded-2xl" onClick={() => setFontScale(1)} aria-label="Fonte padrão">Padrão</Button>
                <Button variant="secondary" className="rounded-2xl" onClick={() => setFontScale(v => Math.min(1.5, +(v + 0.125).toFixed(3)))} aria-label="Aumentar fonte">A+</Button>
              </div>
              <div className="flex items-center gap-2">
                <LineHeight className="h-4 w-4" aria-hidden="true" />
                <Input type="range" min={1.2} max={2} step={0.1} value={lineSpace} onChange={(e) => setLineSpace(Number(e.target.value))} aria-label="Espaçamento entre linhas" />
              </div>
              <div className="flex items-center gap-2">
                <span>Leitura facilitada</span>
                <Switch id="dys" checked={dyslexia} onCheckedChange={setDyslexia} aria-label="Ativar leitura facilitada" />
              </div>
            </CardContent>
          </Card>

          <Card className="shadow-sm">
            <CardHeader className="pb-2">
              <CardTitle className="text-base flex items-center gap-2"><Search className="h-4 w-4" /> Busca</CardTitle>
            </CardHeader>
            <CardContent className="flex items-center gap-2">
              <Input
                value={query}
                onChange={(e) => setQuery(e.target.value)}
                placeholder="Busque por temas (ex.: primeira infância)"
                aria-label="Buscar conteúdo"
                className="flex-1"
              />
              <Button variant="secondary" className="rounded-2xl" onClick={() => setQuery("")} aria-label="Limpar busca">Limpar</Button>
              <Save className="h-4 w-4" aria-hidden title="Preferências são salvas automaticamente" />
            </CardContent>
          </Card>
        </div>
        <div aria-live="polite" className="sr-only">{announce}</div>
      </section>

      {/* Conteúdo principal */}
      <main id="conteudo" className="mx-auto max-w-6xl px-4 py-10">
        <motion.section initial={{ opacity: 0, y: 10 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.4 }} className="mb-10">
          <h1 className="text-3xl md:text-4xl font-bold tracking-tight">Desenvolvimento Social com Acessibilidade</h1>
          <p className="mt-3 max-w-3xl text-lg text-zinc-700 dark:text-zinc-300">
            Este site demonstra boas práticas de design inclusivo para disseminar conhecimento sobre políticas e iniciativas de desenvolvimento social.
            Explore os conteúdos abaixo e personalize sua experiência visual nas configurações de acessibilidade.
          </p>
        </motion.section>

        <div className="grid gap-6 md:grid-cols-2">
          {filtered.map((s, i) => (
            <motion.section key={s.id} id={s.id} initial={{ opacity: 0, y: 16 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.35, delay: i * 0.05 }}>
              <Card className="h-full shadow-sm">
                <CardHeader>
                  <CardTitle className="flex items-center gap-2 text-xl">
                    <s.icon className="h-5 w-5" aria-hidden="true" /> {s.title}
                  </CardTitle>
                </CardHeader>
                <CardContent>
                  <p className="text-zinc-700 dark:text-zinc-300">{s.text}</p>
                  {/* Conteúdo complementar acessível */}
                  {s.id === "programas" && (
                    <ul className="mt-4 list-disc pl-6 space-y-1">
                      <li>Centros de Referência de Assistência Social (CRAS) e Serviços de Convivência.</li>
                      <li>Incentivo à economia solidária e ao microcrédito produtivo.</li>
                      <li>Parcerias intersetoriais: saúde, educação, trabalho e cultura.</li>
                    </ul>
                  )}
                  {s.id === "indicadores" && (
                    <details className="mt-4">
                      <summary className="cursor-pointer underline">Como monitorar indicadores com participação social</summary>
                      <p className="mt-2">Crie painéis públicos com dados abertos, promova audiências, e publique relatórios com linguagem simples e recursos visuais acessíveis.</p>
                    </details>
                  )}
                </CardContent>
              </Card>
            </motion.section>
          ))}
        </div>

        {/* Chamada à ação */}
        <motion.section initial={{ opacity: 0, y: 16 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.35, delay: 0.15 }} className="mt-12">
          <Card className="shadow-sm">
            <CardContent className="py-6 md:py-8">
              <div className="md:flex items-center justify-between gap-6">
                <p className="text-lg max-w-3xl">
                  Quer apoiar iniciativas locais de desenvolvimento social? Cadastre-se para receber oportunidades de voluntariado, oficinas e editais.
                </p>
                <form className="mt-4 md:mt-0 flex gap-2" aria-label="Cadastro de interesse">
                  <Input type="email" required placeholder="Seu e‑mail" aria-label="Digite seu e‑mail" />
                  <Button type="submit" className="rounded-2xl">Quero participar</Button>
                </form>
              </div>
            </CardContent>
          </Card>
        </motion.section>
      </main>

      <footer className="mt-10 border-t">
        <div className="mx-auto max-w-6xl px-4 py-8 text-sm text-zinc-600 dark:text-zinc-400">
          <p>
            © {new Date().getFullYear()} Rede de Desenvolvimento Social. Conteúdo em linguagem simples, com foco em acessibilidade e inclusão.
          </p>
          <p className="mt-2">
            Siga boas práticas: contraste suficiente, navegação por teclado, textos alternativos em imagens, rótulos claros em formulários e semântica HTML.
          </p>
        </div>
      </footer>
    </div>
  );
}
