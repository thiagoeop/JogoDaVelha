<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jogo da Velha Moderno</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        .cell {
            transition: all 0.3s ease;
        }
        .cell:hover {
            transform: scale(1.05);
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
        }
        .winning-cell {
            animation: pulse 1.5s infinite;
        }
        @keyframes pulse {
            0% { background-color: rgba(134, 239, 172, 0.5); }
            50% { background-color: rgba(74, 222, 128, 0.8); }
            100% { background-color: rgba(134, 239, 172, 0.5); }
        }
        .modal {
            transition: opacity 0.3s ease, transform 0.3s ease;
        }
        .modal-enter {
            opacity: 0;
            transform: scale(0.9);
        }
        .modal-enter-active {
            opacity: 1;
            transform: scale(1);
        }
        .ranking-item {
            transition: all 0.3s ease;
        }
        .ranking-item:hover {
            transform: translateX(5px);
        }
        .stats-card {
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
    </style>
</head>
<body class="bg-gradient-to-br from-blue-50 to-purple-50 min-h-screen flex flex-col items-center justify-center p-4">
    <div class="max-w-4xl w-full">
        <h1 class="text-4xl font-bold text-center mb-2 text-transparent bg-clip-text bg-gradient-to-r from-purple-600 to-blue-500">
            JOGO DA VELHA
        </h1>
        
        <div class="flex flex-col md:flex-row gap-6">
            <!-- Main game area -->
            <div class="flex-1">
                <div class="flex justify-between items-center mb-6">
                    <div class="flex items-center space-x-2">
                        <div class="w-10 h-10 rounded-full bg-blue-100 flex items-center justify-center">
                            <i class="fas fa-times text-blue-600 text-xl"></i>
                        </div>
                        <span class="font-medium text-gray-700">Jogador X</span>
                    </div>
                    
                    <div class="bg-white rounded-full px-4 py-2 shadow-sm">
                        <span class="font-bold text-gray-700">Vez: <span id="current-player" class="text-blue-600">X</span></span>
                    </div>
                    
                    <div class="flex items-center space-x-2">
                        <div class="w-10 h-10 rounded-full bg-pink-100 flex items-center justify-center">
                            <i class="far fa-circle text-pink-500 text-xl"></i>
                        </div>
                        <span class="font-medium text-gray-700">Jogador O</span>
                    </div>
                </div>
                
                <div class="grid grid-cols-3 gap-3 mb-6">
                    <!-- Game board will be generated by JavaScript -->
                </div>
                
                <div class="flex justify-center space-x-4">
                    <button id="reset-btn" class="bg-white hover:bg-gray-100 text-gray-800 font-semibold py-2 px-4 border border-gray-300 rounded-lg shadow-sm transition">
                        <i class="fas fa-redo mr-2"></i> Reiniciar
                    </button>
                    <button id="theme-btn" class="bg-gray-800 hover:bg-gray-700 text-white font-semibold py-2 px-4 rounded-lg shadow-sm transition">
                        <i class="fas fa-moon mr-2"></i> Tema Escuro
                    </button>
                    <button id="stats-btn" class="bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2 px-4 rounded-lg shadow-sm transition">
                        <i class="fas fa-trophy mr-2"></i> Estatísticas
                    </button>
                </div>
            </div>
            
            <!-- Stats sidebar -->
            <div id="stats-sidebar" class="hidden md:block w-full md:w-64 bg-white rounded-xl p-4 shadow-lg h-fit transition-all duration-300">
                <h2 class="text-xl font-bold text-gray-800 mb-4 flex items-center">
                    <i class="fas fa-trophy text-yellow-500 mr-2"></i> Ranking
                </h2>
                
                <div class="mb-6">
                    <div class="flex justify-between items-center mb-2">
                        <h3 class="font-medium text-gray-700">Vitórias:</h3>
                        <div class="flex space-x-4">
                            <span class="text-blue-600 font-bold" id="x-wins">0</span>
                            <span class="text-pink-500 font-bold" id="o-wins">0</span>
                        </div>
                    </div>
                    <div class="flex justify-between items-center">
                        <h3 class="font-medium text-gray-700">Empates:</h3>
                        <span class="text-gray-600 font-bold" id="draws">0</span>
                    </div>
                </div>
                
                <div class="space-y-3">
                    <div class="ranking-item flex items-center justify-between bg-blue-50 p-3 rounded-lg">
                        <div class="flex items-center">
                            <div class="w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center mr-2">
                                <i class="fas fa-times text-blue-600 text-sm"></i>
                            </div>
                            <span class="font-medium">Jogador X</span>
                        </div>
                        <span class="bg-blue-100 text-blue-800 text-xs font-semibold px-2.5 py-0.5 rounded-full" id="x-wins-count">0</span>
                    </div>
                    
                    <div class="ranking-item flex items-center justify-between bg-pink-50 p-3 rounded-lg">
                        <div class="flex items-center">
                            <div class="w-8 h-8 rounded-full bg-pink-100 flex items-center justify-center mr-2">
                                <i class="far fa-circle text-pink-500 text-sm"></i>
                            </div>
                            <span class="font-medium">Jogador O</span>
                        </div>
                        <span class="bg-pink-100 text-pink-800 text-xs font-semibold px-2.5 py-0.5 rounded-full" id="o-wins-count">0</span>
                    </div>
                    
                    <div class="ranking-item flex items-center justify-between bg-gray-100 p-3 rounded-lg">
                        <div class="flex items-center">
                            <div class="w-8 h-8 rounded-full bg-gray-200 flex items-center justify-center mr-2">
                                <i class="fas fa-handshake text-gray-600 text-sm"></i>
                            </div>
                            <span class="font-medium">Empates</span>
                        </div>
                        <span class="bg-gray-200 text-gray-800 text-xs font-semibold px-2.5 py-0.5 rounded-full" id="draws-count">0</span>
                    </div>
                </div>
                
                <div class="mt-6">
                    <button id="reset-stats-btn" class="w-full bg-gray-200 hover:bg-gray-300 text-gray-800 font-medium py-2 px-4 rounded-lg transition flex items-center justify-center">
                        <i class="fas fa-trash-alt mr-2"></i> Limpar Estatísticas
                    </button>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Winner Modal -->
    <div id="winner-modal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 hidden modal">
        <div class="bg-white rounded-xl p-6 max-w-sm w-full shadow-xl transform transition-all">
            <div class="text-center">
                <div class="mx-auto flex items-center justify-center h-16 w-16 rounded-full bg-green-100 mb-4">
                    <i id="winner-icon" class="fas fa-trophy text-2xl text-yellow-500"></i>
                </div>
                <h3 class="text-2xl font-bold text-gray-900 mb-2" id="winner-text">Vitória do X!</h3>
                <p class="text-gray-600 mb-6" id="winner-message">Parabéns, jogador X venceu!</p>
                <div class="flex justify-center space-x-3">
                    <button id="play-again-btn" class="bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-6 rounded-lg transition">
                        Jogar Novamente
                    </button>
                    <button id="close-modal-btn" class="bg-white hover:bg-gray-100 text-gray-800 font-medium py-2 px-6 border border-gray-300 rounded-lg transition">
                        Fechar
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Mobile Stats Modal -->
    <div id="mobile-stats-modal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 hidden modal">
        <div class="bg-white rounded-xl p-6 max-w-sm w-full shadow-xl transform transition-all">
            <div class="text-center">
                <h3 class="text-xl font-bold text-gray-900 mb-4 flex items-center justify-center">
                    <i class="fas fa-trophy text-yellow-500 mr-2"></i> Estatísticas
                </h3>
                
                <div class="mb-6">
                    <div class="flex justify-between items-center mb-2">
                        <h3 class="font-medium text-gray-700">Vitórias:</h3>
                        <div class="flex space-x-4">
                            <span class="text-blue-600 font-bold" id="mobile-x-wins">0</span>
                            <span class="text-pink-500 font-bold" id="mobile-o-wins">0</span>
                        </div>
                    </div>
                    <div class="flex justify-between items-center">
                        <h3 class="font-medium text-gray-700">Empates:</h3>
                        <span class="text-gray-600 font-bold" id="mobile-draws">0</span>
                    </div>
                </div>
                
                <div class="space-y-3 mb-6">
                    <div class="ranking-item flex items-center justify-between bg-blue-50 p-3 rounded-lg">
                        <div class="flex items-center">
                            <div class="w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center mr-2">
                                <i class="fas fa-times text-blue-600 text-sm"></i>
                            </div>
                            <span class="font-medium">Jogador X</span>
                        </div>
                        <span class="bg-blue-100 text-blue-800 text-xs font-semibold px-2.5 py-0.5 rounded-full" id="mobile-x-wins-count">0</span>
                    </div>
                    
                    <div class="ranking-item flex items-center justify-between bg-pink-50 p-3 rounded-lg">
                        <div class="flex items-center">
                            <div class="w-8 h-8 rounded-full bg-pink-100 flex items-center justify-center mr-2">
                                <i class="far fa-circle text-pink-500 text-sm"></i>
                            </div>
                            <span class="font-medium">Jogador O</span>
                        </div>
                        <span class="bg-pink-100 text-pink-800 text-xs font-semibold px-2.5 py-0.5 rounded-full" id="mobile-o-wins-count">0</span>
                    </div>
                    
                    <div class="ranking-item flex items-center justify-between bg-gray-100 p-3 rounded-lg">
                        <div class="flex items-center">
                            <div class="w-8 h-8 rounded-full bg-gray-200 flex items-center justify-center mr-2">
                                <i class="fas fa-handshake text-gray-600 text-sm"></i>
                            </div>
                            <span class="font-medium">Empates</span>
                        </div>
                        <span class="bg-gray-200 text-gray-800 text-xs font-semibold px-2.5 py-0.5 rounded-full" id="mobile-draws-count">0</span>
                    </div>
                </div>
                
                <div class="flex justify-center space-x-3">
                    <button id="reset-stats-modal-btn" class="bg-gray-200 hover:bg-gray-300 text-gray-800 font-medium py-2 px-4 rounded-lg transition">
                        <i class="fas fa-trash-alt mr-2"></i> Limpar
                    </button>
                    <button id="close-stats-modal-btn" class="bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-lg transition">
                        <i class="fas fa-times mr-2"></i> Fechar
                    </button>
                </div>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // Game state
            let board = ['', '', '', '', '', '', '', '', ''];
            let currentPlayer = 'X';
            let gameActive = true;
            let darkMode = false;
            
            // Statistics
            let stats = {
                xWins: 0,
                oWins: 0,
                draws: 0
            };
            
            // Load stats from localStorage if available
            if (localStorage.getItem('ticTacToeStats')) {
                stats = JSON.parse(localStorage.getItem('ticTacToeStats'));
                updateStatsUI();
            }
            
            // DOM elements
            const boardElement = document.querySelector('.grid');
            const currentPlayerElement = document.getElementById('current-player');
            const resetButton = document.getElementById('reset-btn');
            const themeButton = document.getElementById('theme-btn');
            const statsButton = document.getElementById('stats-btn');
            const statsSidebar = document.getElementById('stats-sidebar');
            const winnerModal = document.getElementById('winner-modal');
            const winnerText = document.getElementById('winner-text');
            const winnerMessage = document.getElementById('winner-message');
            const winnerIcon = document.getElementById('winner-icon');
            const playAgainButton = document.getElementById('play-again-btn');
            const closeModalButton = document.getElementById('close-modal-btn');
            const resetStatsButton = document.getElementById('reset-stats-btn');
            const mobileStatsModal = document.getElementById('mobile-stats-modal');
            const closeStatsModalButton = document.getElementById('close-stats-modal-btn');
            const resetStatsModalButton = document.getElementById('reset-stats-modal-btn');
            
            // Winning combinations
            const winningConditions = [
                [0, 1, 2], [3, 4, 5], [6, 7, 8], // rows
                [0, 3, 6], [1, 4, 7], [2, 5, 8], // columns
                [0, 4, 8], [2, 4, 6]             // diagonals
            ];
            
            // Initialize the game board
            function initializeBoard() {
                boardElement.innerHTML = '';
                
                for (let i = 0; i < 9; i++) {
                    const cell = document.createElement('div');
                    cell.className = 'cell bg-white rounded-lg shadow-md h-24 flex items-center justify-center cursor-pointer text-4xl font-bold transition';
                    cell.dataset.index = i;
                    cell.addEventListener('click', () => handleCellClick(i));
                    boardElement.appendChild(cell);
                }
            }
            
            // Handle cell click
            function handleCellClick(index) {
                if (!gameActive || board[index] !== '') return;
                
                board[index] = currentPlayer;
                updateBoard();
                
                if (checkWin()) {
                    handleWin();
                    return;
                }
                
                if (checkDraw()) {
                    handleDraw();
                    return;
                }
                
                switchPlayer();
            }
            
            // Update the board UI
            function updateBoard() {
                const cells = document.querySelectorAll('.cell');
                
                cells.forEach((cell, index) => {
                    if (board[index] !== '') {
                        cell.innerHTML = board[index] === 'X' 
                            ? '<i class="fas fa-times text-blue-600"></i>' 
                            : '<i class="far fa-circle text-pink-500"></i>';
                        cell.classList.add('pointer-events-none');
                    } else {
                        cell.innerHTML = '';
                        cell.classList.remove('pointer-events-none');
                    }
                });
            }
            
            // Switch player
            function switchPlayer() {
                currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
                currentPlayerElement.textContent = currentPlayer;
                currentPlayerElement.className = currentPlayer === 'X' ? 'text-blue-600' : 'text-pink-500';
            }
            
            // Check for win
            function checkWin() {
                return winningConditions.some(condition => {
                    return condition.every(index => {
                        return board[index] === currentPlayer;
                    });
                });
            }
            
            // Check for draw
            function checkDraw() {
                return board.every(cell => cell !== '');
            }
            
            // Handle win
            function handleWin() {
                gameActive = false;
                
                // Update statistics
                if (currentPlayer === 'X') {
                    stats.xWins++;
                } else {
                    stats.oWins++;
                }
                saveStats();
                updateStatsUI();
                
                // Highlight winning cells
                const winningCombination = winningConditions.find(condition => {
                    return condition.every(index => {
                        return board[index] === currentPlayer;
                    });
                });
                
                winningCombination.forEach(index => {
                    document.querySelector(`.cell[data-index="${index}"]`).classList.add('winning-cell');
                });
                
                // Show winner modal
                winnerText.textContent = `Vitória do ${currentPlayer}!`;
                winnerMessage.textContent = `Parabéns, jogador ${currentPlayer} venceu!`;
                
                if (currentPlayer === 'X') {
                    winnerIcon.className = 'fas fa-times text-blue-600 text-2xl';
                } else {
                    winnerIcon.className = 'far fa-circle text-pink-500 text-2xl';
                }
                
                winnerModal.classList.remove('hidden');
            }
            
            // Handle draw
            function handleDraw() {
                gameActive = false;
                stats.draws++;
                saveStats();
                updateStatsUI();
                
                // Show draw modal
                winnerText.textContent = 'Empate!';
                winnerMessage.textContent = 'Ninguém venceu desta vez. Tente novamente!';
                winnerIcon.className = 'fas fa-handshake text-gray-500 text-2xl';
                
                winnerModal.classList.remove('hidden');
            }
            
            // Reset game
            function resetGame() {
                board = ['', '', '', '', '', '', '', '', ''];
                currentPlayer = 'X';
                gameActive = true;
                
                currentPlayerElement.textContent = currentPlayer;
                currentPlayerElement.className = 'text-blue-600';
                
                // Remove winning cell highlights
                document.querySelectorAll('.cell').forEach(cell => {
                    cell.classList.remove('winning-cell');
                });
                
                updateBoard();
            }
            
            // Toggle dark mode
            function toggleDarkMode() {
                darkMode = !darkMode;
                
                if (darkMode) {
                    document.body.classList.remove('bg-gradient-to-br', 'from-blue-50', 'to-purple-50');
                    document.body.classList.add('bg-gray-900');
                    themeButton.innerHTML = '<i class="fas fa-sun mr-2"></i> Tema Claro';
                    themeButton.classList.remove('bg-gray-800', 'hover:bg-gray-700');
                    themeButton.classList.add('bg-yellow-500', 'hover:bg-yellow-600');
                } else {
                    document.body.classList.add('bg-gradient-to-br', 'from-blue-50', 'to-purple-50');
                    document.body.classList.remove('bg-gray-900');
                    themeButton.innerHTML = '<i class="fas fa-moon mr-2"></i> Tema Escuro';
                    themeButton.classList.add('bg-gray-800', 'hover:bg-gray-700');
                    themeButton.classList.remove('bg-yellow-500', 'hover:bg-yellow-600');
                }
            }
            
            // Toggle stats sidebar
            function toggleStatsSidebar() {
                if (window.innerWidth >= 768) {
                    statsSidebar.classList.toggle('hidden');
                } else {
                    // On mobile, show modal instead
                    updateMobileStatsUI();
                    mobileStatsModal.classList.remove('hidden');
                }
            }
            
            // Update statistics UI
            function updateStatsUI() {
                document.getElementById('x-wins').textContent = stats.xWins;
                document.getElementById('o-wins').textContent = stats.oWins;
                document.getElementById('draws').textContent = stats.draws;
                document.getElementById('x-wins-count').textContent = stats.xWins;
                document.getElementById('o-wins-count').textContent = stats.oWins;
                document.getElementById('draws-count').textContent = stats.draws;
            }
            
            // Update mobile statistics UI
            function updateMobileStatsUI() {
                document.getElementById('mobile-x-wins').textContent = stats.xWins;
                document.getElementById('mobile-o-wins').textContent = stats.oWins;
                document.getElementById('mobile-draws').textContent = stats.draws;
                document.getElementById('mobile-x-wins-count').textContent = stats.xWins;
                document.getElementById('mobile-o-wins-count').textContent = stats.oWins;
                document.getElementById('mobile-draws-count').textContent = stats.draws;
            }
            
            // Save stats to localStorage
            function saveStats() {
                localStorage.setItem('ticTacToeStats', JSON.stringify(stats));
            }
            
            // Reset statistics
            function resetStatistics() {
                stats = {
                    xWins: 0,
                    oWins: 0,
                    draws: 0
                };
                saveStats();
                updateStatsUI();
                updateMobileStatsUI();
            }
            
            // Event listeners
            resetButton.addEventListener('click', resetGame);
            themeButton.addEventListener('click', toggleDarkMode);
            statsButton.addEventListener('click', toggleStatsSidebar);
            playAgainButton.addEventListener('click', () => {
                winnerModal.classList.add('hidden');
                resetGame();
            });
            closeModalButton.addEventListener('click', () => {
                winnerModal.classList.add('hidden');
            });
            resetStatsButton.addEventListener('click', resetStatistics);
            closeStatsModalButton.addEventListener('click', () => {
                mobileStatsModal.classList.add('hidden');
            });
            resetStatsModalButton.addEventListener('click', () => {
                resetStatistics();
                mobileStatsModal.classList.add('hidden');
            });
            
            // Initialize the game
            initializeBoard();
        });
    </script>
</body>
</html>
