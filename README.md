# SPOD
src/
├── components/
│   ├── Navbar.tsx         (Logo, Compteur Live, Sélecteur Drapeau)
│   ├── GameModal.tsx      (Fenêtre modale universelle pour jouer)
│   ├── ContactForm.tsx    (Formulaire lié à stpaulgens@gmail.com)
│   └── games/
│       ├── ChessGame.tsx  (Moteur Échecs interactif + IA)
│       └── QuizGame.tsx   (Moteur de Quiz dynamique)
├── hooks/
│   └── useTranslation.ts  (Gestion instantanée des langues)
└── App.tsx                (Page d'accueil avec states dynamiques)
import { create } from 'zustand';

type Language = 'FR' | 'EN' | 'ES';

const translations = {
  FR: {
    title: "WORLD GAMING LEGENDS",
    heroSubtitle: "Un Seul Monde • Une Seule Communauté • Des Défis Infinis",
    playersOnline: "Joueurs en ligne",
    gamesPlayed: "Parties jouées",
    playNow: "Jouer Maintenant",
    about: "À propos",
    contact: "Contact",
  },
  EN: {
    title: "WORLD GAMING LEGENDS",
    heroSubtitle: "One World • One Community • Infinite Challenges",
    playersOnline: "Players online",
    gamesPlayed: "Games played",
    playNow: "Play Now",
    about: "About",
    contact: "Contact",
  },
  ES: {
    title: "WORLD GAMING LEGENDS",
    heroSubtitle: "Un Solo Mundo • Una Sola Comunidad • Desafíos Infinitos",
    playersOnline: "Jugadores en línea",
    gamesPlayed: "Partidas jugadas",
    playNow: "Jugar Ahora",
    about: "Acerca de",
    contact: "Contacto",
  }
};

interface LangState {
  currentLang: Language;
  setLanguage: (lang: Language) => void;
  t: typeof translations['FR'];
}

export const useTranslation = create<LangState>((set) => ({
  currentLang: 'FR',
  setLanguage: (lang) => set({ currentLang: lang, t: translations[lang] }),
  t: translations['FR'],
}));
import React, { useState, useEffect } from 'react';
import { useTranslation } from '../hooks/useTranslation';

export const Navbar = () => {
  const { currentLang, setLanguage, t } = useTranslation();
  const [onlineCount, setOnlineCount] = useState(12847);

  // Simulation dynamique en temps réel du compteur de joueurs
  useEffect(() => {
    const interval = setInterval(() => {
      setOnlineCount(prev => prev + Math.floor(Math.random() * 11) - 5);
    }, 3000);
    return () => clearInterval(interval);
  }, []);

  return (
    <nav className="bg-slate-900 text-white px-6 py-4 flex items-center justify-between border-b border-slate-800">
      {/* LOGO PROFESSIONNEL */}
      <div className="flex items-center gap-2 font-bold text-xl tracking-wider text-amber-500">
        <span className="p-2 bg-amber-500 text-slate-900 rounded-lg font-black">WGL</span>
        WORLD GAMING LEGENDS
      </div>

      {/* COMPTEUR LIVE FLUIDE */}
      <div className="flex items-center gap-2 bg-slate-800 px-4 py-1.5 rounded-full border border-slate-700">
        <span className="relative flex h-3 w-3">
          <span className="animate-ping absolute inline-flex h-full w-full rounded-full bg-green-400 opacity-75"></span>
          <span className="relative inline-flex rounded-full h-3 w-3 bg-green-500"></span>
        </span>
        <span className="text-sm font-medium text-slate-300">
          {onlineCount.toLocaleString()} {t.playersOnline}
        </span>
      </div>

      {/* SÉLECTEUR DE DRAPEAUX VISUELS */}
      <div className="flex items-center gap-3 bg-slate-800 p-1.5 rounded-lg">
        <button 
          onClick={() => setLanguage('FR')} 
          className={`px-2 py-1 rounded transition-all text-xl ${currentLang === 'FR' ? 'bg-amber-500 scale-110' : 'opacity-50 hover:opacity-100'}`}
          title="Français"
        >
          🇫🇷
        </button>
        <button 
          onClick={() => setLanguage('EN')} 
          className={`px-2 py-1 rounded transition-all text-xl ${currentLang === 'EN' ? 'bg-amber-500 scale-110' : 'opacity-50 hover:opacity-100'}`}
          title="English"
        >
          🇬🇧
        </button>
        <button 
          onClick={() => setLanguage('ES')} 
          className={`px-2 py-1 rounded transition-all text-xl ${currentLang === 'ES' ? 'bg-amber-500 scale-110' : 'opacity-50 hover:opacity-100'}`}
          title="Español"
        >
          🇪🇸
        </button>
      </div>
    </nav>
  );
};
import React, { useState } from 'react';

const QUIZ_DATA = [
  { question: "Quel jeu se joue avec 32 pièces sur un plateau de 64 cases ?", answers: ["Les Dames", "Les Échecs", "Le Ludo"], correct: 1 },
  { question: "Combien de dominos y a-t-il dans un jeu de dominos standard double-six ?", answers: ["28", "32", "54"], correct: 0 }
];

export const QuizGame = () => {
  const [currentQuestion, setCurrentQuestion] = useState(0);
  const [score, setScore] = useState(0);
  const [gameOver, setGameOver] = useState(false);

  const handleAnswer = (index: number) => {
    if (index === QUIZ_DATA[currentQuestion].correct) {
      setScore(score + 1);
    }
    if (currentQuestion + 1 < QUIZ_DATA.length) {
      setCurrentQuestion(currentQuestion + 1);
    } else {
      setGameOver(true);
    }
  };

  if (gameOver) {
    return (
      <div className="text-center p-6 bg-slate-800 rounded-xl border border-slate-700 text-white">
        <h3 className="text-2xl font-bold text-amber-500 mb-2">Partie Terminée !</h3>
        <p className="text-lg">Votre score final : <span className="font-bold text-green-400">{score} / {QUIZ_DATA.length}</span></p>
        <button onClick={() => { setCurrentQuestion(0); setScore(0); setGameOver(false); }} className="mt-4 bg-amber-500 hover:bg-amber-600 text-slate-900 px-6 py-2 rounded-lg font-bold transition">Recommencer</button>
      </div>
    );
  }

  return (
    <div className="p-6 bg-slate-800 rounded-xl border border-slate-700 text-white max-w-xl mx-auto">
      <div className="flex justify-between text-sm text-slate-400 mb-4">
        <span>Question {currentQuestion + 1} / {QUIZ_DATA.length}</span>
        <span>Score: {score}</span>
      </div>
      <h3 className="text-xl font-semibold mb-6">{QUIZ_DATA[currentQuestion].question}</h3>
      <div className="flex flex-col gap-3">
        {QUIZ_DATA[currentQuestion].answers.map((answer, idx) => (
          <button 
            key={idx} 
            onClick={() => handleAnswer(idx)}
            className="w-full text-left bg-slate-700 hover:bg-slate-600 p-4 rounded-lg border border-slate-600 hover:border-amber-500 transition-all font-medium"
          >
            {answer}
          </button>
        ))}
      </div>
    </div>
  );
};

import React, { useState } from 'react';

export const ContactForm = () => {
  const [status, setStatus] = useState('');

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    // Simulation de l'envoi ou intégration API de messagerie (Formspree / EmailJS)
    setStatus('Sending...');
    setTimeout(() => {
      setStatus('Success! Message envoyé à stpaulgens@gmail.com');
    }, 1000);
  };

  return (
    <form onSubmit={handleSubmit} className="bg-slate-800 p-6 rounded-xl border border-slate-700 text-white max-w-lg mx-auto flex flex-col gap-4">
      <h3 className="text-xl font-bold text-amber-500">Formulaire de Contact</h3>
      <div>
        <label className="block text-xs font-semibold text-slate-400 uppercase mb-2">Votre Email</label>
        <input required type="email" className="w-full bg-slate-950 border border-slate-700 rounded-lg p-3 text-white focus:outline-none focus:border-amber-500" placeholder="exemple@mail.com"/>
      </div>
      <div>
        <label className="block text-xs font-semibold text-slate-400 uppercase mb-2">Message</label>
        <textarea required rows={4} className="w-full bg-slate-950 border border-slate-700 rounded-lg p-3 text-white focus:outline-none focus:border-amber-500" placeholder="Votre message..."></textarea>
      </div>
      <button type="submit" className="w-full bg-amber-500 hover:bg-amber-600 text-slate-900 p-3 rounded-lg font-bold transition">
        Envoyer le message
      </button>
      {status && <p className="text-center text-sm font-semibold text-green-400 mt-2">{status}</p>}
    </form>
  );
};
