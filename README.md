export default function PartyMasterGame() {
  const board = Array.from({ length: 60 }, (_, i) => {
    const n = i + 1;

    const special = {
      6: '🔥',
      12: '⬅️',
      16: '🔥',
      22: '➡️',
      26: '🔥',
      32: '⏸️',
      36: '🔥',
      42: '⬅️',
      46: '🔥',
      52: '➡️',
      56: '💀',
      60: '🏆',
    };

    return {
      number: n,
      icon: special[n] || '🎲',
    };
  });

  const questions = [
    '¿Capital de Japón? → Tokio',
    '¿Quién canta Billie Jean? → Michael Jackson',
    '¿Cuántos jugadores tiene un equipo de fútbol? → 11',
    '¿Qué planeta es rojo? → Marte',
    '¿Qué país ganó el Mundial 2010? → España',
    '¿Qué marca tiene una manzana? → Apple',
    '¿Qué país tiene una hoja de arce en su bandera? → Canadá',
    '¿Quién descubrió América? → Cristóbal Colón',
    '¿Qué saga tiene sables láser? → Star Wars',
    '¿Qué animal es el más grande? → Ballena azul',
  ];

  const hardChallenges = [
    'Responde 3 preguntas seguidas correctamente.',
    'Di 10 países en 15 segundos.',
    'Haz 20 sentadillas antes de responder.',
    'Habla sin usar sí ni no.',
    'Adivina una película por mímica.',
    'Nombra 12 capitales en 20 segundos.',
    'Equilibrio sobre un pie mientras respondes.',
    'Superreto: 5 respuestas seguidas.',
  ];

  const [players, setPlayers] = React.useState([
    { name: 'Jugador 1', pos: 1 },
    { name: 'Jugador 2', pos: 1 },
  ]);

  const [current, setCurrent] = React.useState(0);
  const [dice, setDice] = React.useState(null);
  const [message, setMessage] = React.useState('Empieza la partida');

  const rollDice = () => {
    const value = Math.floor(Math.random() * 6) + 1;
    setDice(value);

    setPlayers((prev) => {
      const updated = [...prev];
      let newPos = updated[current].pos + value;

      if (newPos > 60) newPos = 60;

      updated[current].pos = newPos;

      let text = `${updated[current].name} avanzó a la casilla ${newPos}`;

      if ([6, 16, 26, 36, 46, 56].includes(newPos)) {
        const challenge = hardChallenges[Math.floor(Math.random() * hardChallenges.length)];
        text += ` | RETO: ${challenge}`;
      }

      if ([12, 42].includes(newPos)) {
        updated[current].pos -= 3;
        text += ' | Retrocede 3 casillas';
      }

      if ([22, 52].includes(newPos)) {
        updated[current].pos += 2;
        text += ' | Avanza 2 casillas';
      }

      if (newPos === 60) {
        text = `🏆 ${updated[current].name} GANÓ LA PARTIDA`;
      }

      if (![60].includes(newPos)) {
        setCurrent((current + 1) % updated.length);
      }

      setMessage(text);

      return updated;
    });
  };

  const randomQuestion = () => {
    const q = questions[Math.floor(Math.random() * questions.length)];
    setMessage(`🃏 ${q}`);
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-900 to-black text-white p-6">
      <div className="max-w-7xl mx-auto">
        <h1 className="text-5xl font-black text-center mb-2">🎲 PARTY MASTER</h1>
        <p className="text-center text-gray-300 mb-8">
          Juego virtual interactivo estilo party
        </p>

        <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
          <div className="lg:col-span-2">
            <div className="grid grid-cols-5 md:grid-cols-10 gap-2">
              {board.map((cell) => {
                const playerHere = players.findIndex((p) => p.pos === cell.number);

                return (
                  <div
                    key={cell.number}
                    className={`rounded-2xl p-2 h-24 border flex flex-col items-center justify-center text-center shadow-lg transition-all ${
                      cell.number === 60
                        ? 'bg-yellow-500 text-black'
                        : 'bg-white/10 backdrop-blur'
                    }`}
                  >
                    <div className="text-xs">{cell.number}</div>
                    <div className="text-2xl">{cell.icon}</div>

                    {playerHere !== -1 && (
                      <div className="mt-1 text-xs font-bold bg-white text-black px-2 rounded-full">
                        J{playerHere + 1}
                      </div>
                    )}
                  </div>
                );
              })}
            </div>
          </div>

          <div className="space-y-4">
            <div className="bg-white/10 rounded-3xl p-5 backdrop-blur shadow-2xl">
              <h2 className="text-2xl font-bold mb-4">🎮 Turno</h2>

              <div className="mb-3 text-lg">
                {players[current].name}
              </div>

              <button
                onClick={rollDice}
                className="w-full bg-yellow-400 hover:bg-yellow-300 text-black font-black py-4 rounded-2xl text-xl transition-all"
              >
                🎲 Tirar dado
              </button>

              <button
                onClick={randomQuestion}
                className="w-full mt-3 bg-cyan-400 hover:bg-cyan-300 text-black font-black py-4 rounded-2xl text-lg transition-all"
              >
                🃏 Carta aleatoria
              </button>

              <div className="mt-5 text-center text-5xl font-black">
                {dice || '🎲'}
              </div>
            </div>

            <div className="bg-white/10 rounded-3xl p-5 backdrop-blur shadow-2xl">
              <h2 className="text-2xl font-bold mb-3">📢 Evento</h2>
              <p className="text-gray-200 leading-relaxed">{message}</p>
            </div>

            <div className="bg-white/10 rounded-3xl p-5 backdrop-blur shadow-2xl">
              <h2 className="text-2xl font-bold mb-3">🏁 Posiciones</h2>

              <div className="space-y-2">
                {players.map((p, i) => (
                  <div
                    key={i}
                    className="flex items-center justify-between bg-black/30 rounded-xl px-4 py-3"
                  >
                    <span>{p.name}</span>
                    <span className="font-bold">Casilla {p.pos}</span>
                  </div>
                ))}
              </div>
            </div>

            <div className="bg-white/10 rounded-3xl p-5 backdrop-blur shadow-2xl">
              <h2 className="text-2xl font-bold mb-3">📜 Leyenda</h2>
              <div className="grid grid-cols-2 gap-2 text-sm">
                <div>🔥 Reto</div>
                <div>⬅️ Retroceso</div>
                <div>➡️ Avance</div>
                <div>💀 Superreto</div>
                <div>🏆 Meta</div>
                <div>🎲 Normal</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}
