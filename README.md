<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Цифрки</title>
<script src="https://telegram.org/js/telegram-web-app.js"></script>
<style>
* { box-sizing: border-box; user-select: none; -webkit-tap-highlight-color: transparent; margin: 0; padding: 0; }
body { background: #0a0e14; color: white; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; overflow-x: hidden; min-height: 100vh; }
body::before { content: ''; position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: radial-gradient(circle at 20% 10%, rgba(33,150,243,0.12) 0%, transparent 50%), radial-gradient(circle at 80% 80%, rgba(156,39,176,0.08) 0%, transparent 50%); pointer-events: none; z-index: 0; }

.header { display: flex; align-items: center; justify-content: space-between; padding: 12px 14px; position: sticky; top: 0; z-index: 100; backdrop-filter: blur(20px); background: rgba(10,14,20,0.85); gap: 8px; }
.header-left { display: flex; align-items: center; gap: 8px; flex: 1; min-width: 0; }
.back-btn { width: 34px; height: 34px; border-radius: 10px; background: rgba(255,255,255,0.06); border: none; color: #fff; font-size: 18px; cursor: pointer; display: none; align-items: center; justify-content: center; flex-shrink: 0; }
.back-btn.show { display: flex; }
.hamburger { display: flex; flex-direction: column; gap: 3px; cursor: pointer; padding: 5px 3px; flex-shrink: 0; }
.hamburger span { width: 20px; height: 2.5px; background: #8892a6; border-radius: 2px; }
.user-pill { display: flex; align-items: center; gap: 8px; background: rgba(255,255,255,0.05); padding: 5px 10px 5px 5px; border-radius: 30px; min-width: 0; }
.user-avatar { width: 34px; height: 34px; border-radius: 50%; background: linear-gradient(135deg, #42A5F5, #9C27B0); display: flex; align-items: center; justify-content: center; font-size: 16px; overflow: hidden; flex-shrink: 0; }
.user-avatar img { width: 100%; height: 100%; object-fit: cover; }
.user-info { display: flex; flex-direction: column; min-width: 0; }
.user-level { font-size: 11px; color: #8892a6; font-weight: 700; }
.user-xp-bar { width: 100px; height: 4px; background: rgba(255,255,255,0.1); border-radius: 2px; overflow: hidden; margin-top: 2px; }
.user-xp-fill { height: 100%; background: linear-gradient(90deg, #42A5F5, #9C27B0); border-radius: 2px; transition: width 0.4s; }
.header-right { display: flex; align-items: center; gap: 8px; flex-shrink: 0; }
.balance-pill { display: flex; align-items: center; gap: 4px; background: rgba(255,255,255,0.06); padding: 7px 12px; border-radius: 20px; }
.balance-value { font-size: 15px; font-weight: 900; color: #fff; font-variant-numeric: tabular-nums; }

.screen { display: none; padding: 14px; position: relative; z-index: 1; padding-bottom: 30px; }
.screen.active { display: block; }

/* Сетка кнопок */
.grid-menu { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin-bottom: 14px; }
.grid-item { background: rgba(255,255,255,0.04); border-radius: 18px; padding: 14px 6px; text-align: center; font-size: 11px; font-weight: 700; color: #c0c8d8; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 8px; transition: 0.15s; position: relative; }
.grid-item:active { transform: scale(0.95); background: rgba(255,255,255,0.08); }
.grid-icon { width: 46px; height: 46px; border-radius: 14px; display: flex; align-items: center; justify-content: center; font-size: 24px; }
.gi-blue { background: linear-gradient(135deg, #42A5F5, #1565C0); }
.gi-orange { background: linear-gradient(135deg, #FFA726, #E65100); }
.gi-red { background: linear-gradient(135deg, #ef5350, #c62828); }
.gi-pink { background: linear-gradient(135deg, #ec407a, #ad1457); }
.gi-purple { background: linear-gradient(135deg, #AB47BC, #6A1B9A); }
.gi-green { background: linear-gradient(135deg, #66BB6A, #2E7D32); }
.gi-yellow { background: linear-gradient(135deg, #FFCA28, #F57C00); }
.gi-teal { background: linear-gradient(135deg, #26C6DA, #00838F); }
.gi-grey { background: linear-gradient(135deg, #78909c, #37474f); }
.gi-indigo { background: linear-gradient(135deg, #5C6BC0, #283593); }
.badge-red { position: absolute; top: 8px; right: 8px; background: #f44336; width: 10px; height: 10px; border-radius: 50%; }

/* Пропуск */
.pass-row { display: flex; align-items: center; justify-content: space-between; padding: 14px 16px; background: linear-gradient(135deg, rgba(30,40,60,0.9), rgba(20,25,40,0.9)); border-radius: 16px; margin-bottom: 10px; cursor: pointer; }
.pass-left { display: flex; align-items: center; gap: 12px; }
.pass-icon { width: 38px; height: 38px; border-radius: 10px; background: linear-gradient(135deg, #FFCA28, #F57C00); display: flex; align-items: center; justify-content: center; font-size: 18px; }
.pass-title { font-size: 14px; font-weight: 800; }
.pass-rank { font-size: 12px; color: #FFCA28; font-weight: 700; }

/* Магазин */
.shop-banner { display: flex; align-items: center; justify-content: space-between; padding: 16px; background: linear-gradient(135deg, rgba(60,45,20,0.95), rgba(35,25,15,0.95)); border: 1px solid rgba(255,193,7,0.3); border-radius: 18px; margin-bottom: 20px; cursor: pointer; position: relative; overflow: hidden; }
.shop-banner::before { content: ''; position: absolute; top: -50%; left: -50%; width: 200%; height: 200%; background: radial-gradient(circle, rgba(255,215,0,0.15) 0%, transparent 60%); pointer-events: none; }
.shop-left { display: flex; align-items: center; gap: 12px; position: relative; z-index: 1; }
.shop-star { width: 42px; height: 42px; border-radius: 12px; background: linear-gradient(135deg, #FFCA28, #F57C00); display: flex; align-items: center; justify-content: center; font-size: 22px; }
.shop-info-title { font-size: 16px; font-weight: 900; color: #FFCA28; }
.shop-info-desc { font-size: 11px; color: #a08858; margin-top: 2px; }
.shop-arrow { font-size: 20px; color: #FFCA28; position: relative; z-index: 1; }

/* Секция "Сейчас" */
.section-header { font-size: 18px; font-weight: 900; margin: 20px 0 10px; color: #fff; }
.now-list { display: flex; flex-direction: column; gap: 8px; }
.now-item { display: flex; align-items: center; gap: 12px; padding: 12px 14px; background: rgba(255,255,255,0.04); border-radius: 14px; cursor: pointer; transition: 0.15s; }
.now-item:active { background: rgba(255,255,255,0.08); }
.now-icon { width: 36px; height: 36px; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 18px; flex-shrink: 0; }
.now-title { flex: 1; font-size: 14px; font-weight: 700; color: #fff; }
.now-value { font-size: 12px; font-weight: 700; text-align: right; }
.now-value.gold { color: #FFCA28; }
.now-value.green { color: #66BB6A; }
.now-value.blue { color: #42A5F5; }
.now-value.purple { color: #AB47BC; }
.now-value.grey { color: #8892a6; }
.now-value.danger { color: #ef5350; }

/* Число/карточка */
.num-card { background: linear-gradient(160deg, rgba(40,50,70,0.85), rgba(20,25,40,0.85)); border-radius: 20px; padding: 22px; margin: 14px 0; border: 1px solid rgba(255,255,255,0.08); position: relative; overflow: hidden; }
.num-card.show { animation: popIn 0.4s; }
@keyframes popIn { 0% { transform: scale(0.9); opacity: 0; } 100% { transform: scale(1); opacity: 1; } }
.num-card.legendary { border-color: #FFD700; box-shadow: 0 0 40px rgba(255,215,0,0.4); }
.num-card.epic { border-color: #9C27B0; box-shadow: 0 0 30px rgba(156,39,176,0.35); }
.num-card.rare { border-color: #42A5F5; box-shadow: 0 0 25px rgba(33,150,243,0.3); }
.num-top { display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px; }
.num-op { display: flex; align-items: center; gap: 10px; }
.num-op-logo { width: 38px; height: 38px; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 16px; font-weight: 900; color: white; }
.num-op-name { font-size: 15px; font-weight: 800; }
.num-sim { width: 42px; height: 30px; background: linear-gradient(135deg, #e8c97a, #c9a548); border-radius: 6px; border: 2px solid #a08830; position: relative; }
.num-sim::before { content: ''; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); width: 20px; height: 14px; border: 1.5px solid rgba(0,0,0,0.2); }
.num-value { font-family: 'Courier New', monospace; font-size: 28px; font-weight: 900; text-align: center; margin: 18px 0; color: #fff; letter-spacing: 1px; word-break: break-all; }
.num-value.spinning { opacity: 0.5; }
.num-tags-row { display: flex; gap: 8px; }
.num-tag-box { flex: 1; background: rgba(0,0,0,0.3); border-radius: 12px; padding: 10px 6px; text-align: center; }
.num-tag-label { font-size: 10px; color: #8892a6; margin-bottom: 3px; }
.num-tag-value { font-size: 12px; font-weight: 800; color: #fff; }
.num-rarity-wrap { text-align: center; margin-top: 16px; }
.num-rarity-badge { font-size: 13px; font-weight: bold; padding: 6px 14px; border-radius: 10px; display: inline-block; color: white; }
.num-price-display { font-size: 26px; font-weight: 900; color: #66BB6A; margin-top: 10px; }
.num-reason { margin-top: 8px; font-size: 12px; color: #8892a6; text-align: center; }

.btn-spin { width: 100%; padding: 22px; font-size: 17px; font-weight: 900; border: none; border-radius: 18px; background: linear-gradient(135deg, #42A5F5, #1565C0); color: white; margin-top: 12px; cursor: pointer; box-shadow: 0 8px 25px rgba(33,150,243,0.35); letter-spacing: 1px; }
.btn-spin:active { transform: scale(0.98); }
.btn-spin:disabled { opacity: 0.5; }

.actions { display: flex; gap: 10px; margin-top: 12px; }
.actions button { flex: 1; padding: 16px; border: none; border-radius: 16px; font-weight: 900; font-size: 14px; cursor: pointer; color: white; }
.btn-sell { background: linear-gradient(135deg, #66BB6A, #2E7D32); }
.btn-keep { background: linear-gradient(135deg, #FFA726, #E65100); color: #000; }

/* Рейтинг */
.season-banner { background: linear-gradient(135deg, #FFCA28 0%, #F57C00 100%); border-radius: 18px; padding: 18px; display: flex; justify-content: space-between; align-items: center; margin-bottom: 14px; }
.season-label { font-size: 12px; font-weight: 900; color: #5a3a00; letter-spacing: 2px; }
.season-name { font-size: 20px; font-weight: 900; color: #2a1a00; }
.tabs { display: flex; gap: 4px; background: rgba(255,255,255,0.05); border-radius: 14px; padding: 4px; margin-bottom: 12px; }
.tab { flex: 1; padding: 10px 6px; text-align: center; font-size: 12px; font-weight: 800; color: #8892a6; border-radius: 10px; cursor: pointer; }
.tab.active { background: rgba(255,255,255,0.1); color: #fff; }
.filter-tabs { display: flex; gap: 6px; overflow-x: auto; margin-bottom: 12px; padding-bottom: 4px; }
.filter-tabs::-webkit-scrollbar { display: none; }
.filter-tab { padding: 8px 14px; background: rgba(255,255,255,0.05); border-radius: 20px; font-size: 12px; font-weight: 700; color: #8892a6; white-space: nowrap; cursor: pointer; }
.filter-tab.active { background: #fff; color: #0a0e14; }
.my-rank-card { background: linear-gradient(135deg, rgba(255,202,40,0.15), rgba(245,124,0,0.08)); border: 1px solid rgba(255,202,40,0.3); border-radius: 16px; padding: 14px; display: flex; align-items: center; gap: 12px; margin-bottom: 14px; }
.my-rank-num { font-size: 20px; font-weight: 900; color: #FFCA28; min-width: 36px; }
.my-rank-avatar { width: 40px; height: 40px; border-radius: 50%; background: rgba(0,0,0,0.4); display: flex; align-items: center; justify-content: center; font-size: 20px; overflow: hidden; }
.my-rank-avatar img { width: 100%; height: 100%; object-fit: cover; }
.my-rank-name { flex: 1; font-size: 14px; font-weight: 800; color: #fff; }
.my-rank-value { font-size: 16px; font-weight: 900; color: #FFCA28; font-variant-numeric: tabular-nums; }
.top-list { display: flex; flex-direction: column; gap: 4px; }
.top-item { display: flex; align-items: center; gap: 10px; padding: 12px 14px; background: rgba(255,255,255,0.04); border-radius: 12px; }
.top-item.me { background: linear-gradient(135deg, rgba(255,202,40,0.15), rgba(245,124,0,0.08)); }
.top-item-rank { font-size: 14px; font-weight: 900; width: 30px; color: #8892a6; }
.top-item-rank.top1 { color: #FFCA28; }
.top-item-rank.top2 { color: #B0BEC5; }
.top-item-rank.top3 { color: #BF8970; }
.top-item-avatar { width: 36px; height: 36px; border-radius: 50%; background: rgba(0,0,0,0.4); display: flex; align-items: center; justify-content: center; font-size: 18px; overflow: hidden; flex-shrink: 0; }
.top-item-avatar img { width: 100%; height: 100%; object-fit: cover; }
.top-item-name { flex: 1; font-size: 14px; font-weight: 800; color: #fff; }
.top-item-vip { color: #FFCA28; font-size: 11px; font-weight: 900; margin-left: 4px; }
.top-item-value { font-size: 14px; font-weight: 900; color: #fff; font-variant-numeric: tabular-nums; }

/* Коллекция */
.coll-item { display: flex; align-items: center; justify-content: space-between; background: rgba(255,255,255,0.04); border-radius: 14px; padding: 14px; margin-bottom: 8px; border-left: 4px solid #4a5568; }
.coll-item.rare { border-left-color: #42A5F5; }
.coll-item.epic { border-left-color: #9C27B0; }
.coll-item.legendary { border-left-color: #FFD700; }
.coll-num { font-family: monospace; font-weight: 900; font-size: 14px; color: #fff; }
.coll-rarity { font-size: 11px; color: #8892a6; margin-top: 3px; }
.coll-price { font-size: 13px; font-weight: 900; color: #66BB6A; }

/* Пусто */
.empty { text-align: center; color: #6a7590; padding: 50px 20px; font-size: 14px; }
.empty-icon { font-size: 50px; margin-bottom: 12px; opacity: 0.4; }

/* Уведомление */
.floating { position: fixed; bottom: 30px; left: 50%; transform: translateX(-50%); background: linear-gradient(135deg, #42A5F5, #9C27B0); color: white; padding: 12px 22px; border-radius: 22px; font-weight: 900; font-size: 14px; z-index: 60; animation: slideUpFade 2s forwards; pointer-events: none; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
@keyframes slideUpFade { 0% { opacity: 0; transform: translate(-50%, 30px) scale(0.8); } 20% { opacity: 1; transform: translate(-50%, 0) scale(1); } 80% { opacity: 1; transform: translate(-50%, -20px); } 100% { opacity: 0; transform: translate(-50%, -50px); } }

/* Боковое меню */
.side-menu { position: fixed; top: 0; left: -300px; width: 280px; height: 100%; background: linear-gradient(180deg, #141a24 0%, #0a0e14 100%); z-index: 400; transition: left 0.3s; padding: 20px 16px; box-shadow: 10px 0 40px rgba(0,0,0,0.8); overflow-y: auto; }
.side-menu.open { left: 0; }
.menu-close { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; font-size: 18px; font-weight: 900; }
.menu-close span:last-child { cursor: pointer; color: #8892a6; }
.menu-item { padding: 13px 16px; margin-bottom: 4px; background: rgba(255,255,255,0.04); border-radius: 12px; cursor: pointer; display: flex; align-items: center; gap: 12px; font-size: 14px; font-weight: 700; color: #c0c8d8; }
.menu-item:active { background: rgba(255,255,255,0.08); }
.overlay-bg { display: none; position: fixed; top: 0; left: 0; right: 0; bottom: 0; background: rgba(0,0,0,0.7); z-index: 350; }
.overlay-bg.show { display: block; }
</style>
</head>
<body>

<div class="header">
    <div class="header-left">
        <button class="back-btn" id="backBtn" onclick="goBack()">←</button>
        <div class="hamburger" onclick="toggleMenu()"><span></span><span></span><span></span></div>
        <div class="user-pill">
            <div class="user-avatar" id="userAvatar">👤</div>
            <div class="user-info">
                <div class="user-level">Ур. <span id="headerLevel">1</span></div>
                <div class="user-xp-bar"><div class="user-xp-fill" id="headerXpFill" style="width:0%"></div></div>
            </div>
        </div>
    </div>
    <div class="header-right">
        <div class="balance-pill">
            <span class="balance-value" id="headerBalance">0 ₽</span>
        </div>
    </div>
</div>

<!-- ГЛАВНАЯ -->
<div class="screen active" id="screen-main">
    <div class="grid-menu">
        <div class="grid-item" onclick="switchScreen('collection')">
            <div class="grid-icon gi-blue">📦</div>Коллекция
        </div>
        <div class="grid-item" onclick="playRoulette()">
            <div class="grid-icon gi-orange">🎰</div>Рулетка
        </div>
        <div class="grid-item" onclick="switchScreen('upgrader')">
            <div class="grid-icon gi-red">⬆️</div>Апгрейдер
        </div>
        <div class="grid-item" onclick="claimDaily()">
            <div class="grid-icon gi-pink">🎁</div>Награды
            <div class="badge-red" id="dailyBadge"></div>
        </div>
        <div class="grid-item" onclick="switchScreen('skills')">
            <div class="grid-icon gi-purple">✨</div>Навыки
        </div>
        <div class="grid-item" onclick="switchScreen('trading')">
            <div class="grid-icon gi-green">🏪</div>Торговля
        </div>
        <div class="grid-item" onclick="switchScreen('achievements')">
            <div class="grid-icon gi-yellow">⭐</div>Достижения
        </div>
        <div class="grid-item" onclick="switchScreen('deals')">
            <div class="grid-icon gi-teal">🔀</div>Сделки
        </div>
        <div class="grid-item" onclick="switchScreen('phones')">
            <div class="grid-icon gi-orange">📱</div>Телефоны
        </div>
    </div>

    <div class="pass-row" onclick="showAlert('Пропуск Alpha', 'Сезонный пропуск активен! Выполняй задания — получай награды.')">
        <div class="pass-left">
            <div class="pass-icon">👑</div>
            <div>
                <div class="pass-title">Пропуск · ур. <span id="passLevel">0</span></div>
                <div class="pass-rank">Alpha</div>
            </div>
        </div>
        <div style="color:#8892a6;">›</div>
    </div>

    <div class="shop-banner" onclick="switchScreen('shop')">
        <div class="shop-left">
            <div class="shop-star">⭐</div>
            <div>
                <div class="shop-info-title">Магазин</div>
                <div class="shop-info-desc">оформление, крутки, удача, VIP</div>
            </div>
        </div>
        <div class="shop-arrow">›</div>
    </div>

    <div class="section-header">Сейчас</div>
    <div class="now-list">
        <div class="now-item" onclick="claimDaily()">
            <div class="now-icon gi-pink">🎁</div>
            <div class="now-title">Награда дня</div>
            <div class="now-value gold" id="nowDailyValue">серия <span id="dailyStreak">0</span> дня</div>
        </div>
        <div class="now-item" onclick="buyerOffer()">
            <div class="now-icon gi-yellow">🏷️</div>
            <div class="now-title">Скупщик</div>
            <div class="now-value green" id="buyerMultiplier">до ×2,50</div>
        </div>
        <div class="now-item" onclick="playRoulette()">
            <div class="now-icon gi-green">🎲</div>
            <div class="now-title">Удача</div>
            <div class="now-value blue" id="luckDisplay">×1</div>
        </div>
        <div class="now-item" onclick="playRoulette()">
            <div class="now-icon gi-orange">🎰</div>
            <div class="now-title">Рулетка</div>
            <div class="now-value grey" id="rouletteFree">0 из 2 бесплатно</div>
        </div>
        <div class="now-item" onclick="claimBonus()">
            <div class="now-icon gi-yellow">⏱️</div>
            <div class="now-title">Бонус</div>
            <div class="now-value danger" id="bonusValue">● забрать 50 000 ₽</div>
        </div>
        <div class="now-item" onclick="switchScreen('profile')">
            <div class="now-icon gi-purple">✨</div>
            <div class="now-title">Уровень</div>
            <div class="now-value" id="levelValue">15 · ещё 5525 XP</div>
        </div>
    </div>

    <div class="section-header">Ещё</div>
    <div class="now-list">
        <div class="now-item" onclick="buyerOffer()">
            <div class="now-icon gi-grey">🏷️</div>
            <div class="now-title">Скупщик</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('profile')">
            <div class="now-icon gi-blue">👤</div>
            <div class="now-title">Профиль</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('friends')">
            <div class="now-icon gi-red">👥</div>
            <div class="now-title">Друзья</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('rating')">
            <div class="now-icon gi-yellow">🏆</div>
            <div class="now-title">Рейтинг</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('number_check')">
            <div class="now-icon gi-blue">🔍</div>
            <div class="now-title">Проверка номера</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('salons')">
            <div class="now-icon gi-green">📡</div>
            <div class="now-title">Салоны</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('history')">
            <div class="now-icon gi-indigo">📜</div>
            <div class="now-title">История</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('chances')">
            <div class="now-icon gi-orange">%</div>
            <div class="now-title">Шансы</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('promo')">
            <div class="now-icon gi-purple">🎟️</div>
            <div class="now-title">Промокод</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('rules')">
            <div class="now-icon gi-red">ℹ️</div>
            <div class="now-title">Правила</div>
            <div style="color:#8892a6;">›</div>
        </div>
        <div class="now-item" onclick="switchScreen('settings')">
            <div class="now-icon gi-grey">⚙️</div>
            <div class="now-title">Настройки</div>
            <div style="color:#8892a6;">›</div>
        </div>
    </div>
</div>

<!-- РЕЙТИНГ -->
<div class="screen" id="screen-rating">
    <div class="season-banner">
        <div>
            <div class="season-label">СЕЗОН</div>
            <div class="season-name">Alpha</div>
        </div>
    </div>

    <div class="tabs">
        <div class="tab active">Alpha</div>
        <div class="tab">ОБТ · итог</div>
    </div>

    <div class="filter-tabs">
        <div class="filter-tab active" data-filter="season" onclick="setFilter('season')">Сезон</div>
        <div class="filter-tab" data-filter="best" onclick="setFilter('best')">Лучший номер</div>
        <div class="filter-tab" data-filter="rich" onclick="setFilter('rich')">Богачи</div>
        <div class="filter-tab" data-filter="invites" onclick="setFilter('invites')">Приглашения</div>
        <div class="filter-tab" data-filter="level" onclick="setFilter('level')">Уровень</div>
    </div>

    <div class="my-rank-card">
        <div class="my-rank-num" id="myRankNum">—</div>
        <div class="my-rank-avatar" id="myRankAvatar">👤</div>
        <div class="my-rank-name">Вы</div>
        <div class="my-rank-value" id="myRankValue">0 ₽</div>
    </div>

    <div class="top-list" id="topList"></div>
</div>

<!-- КОЛЛЕКЦИЯ -->
<div class="screen" id="screen-collection">
    <div class="section-header">📦 Коллекция</div>
    <div id="collectionList"></div>
</div>

<!-- ПРОФИЛЬ -->
<div class="screen" id="screen-profile">
    <div class="section-header">👤 Профиль</div>
    <div class="num-card" style="text-align:center;">
        <div class="user-avatar" id="profileAvatar" style="width:80px;height:80px;font-size:40px;margin:0 auto 12px;"></div>
        <div style="font-size:22px;font-weight:900;" id="profileName">Гость</div>
        <div style="color:#8892a6;font-size:12px;margin-top:4px;" id="profileId">ID: ---</div>
    </div>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:14px;">
        <div class="num-tag-box"><div class="num-tag-label">ВЫБИТО</div><div class="num-tag-value" id="statSpins">0</div></div>
        <div class="num-tag-box"><div class="num-tag-label">ЗАРАБОТАНО</div><div class="num-tag-value" id="statEarned">0 ₽</div></div>
        <div class="num-tag-box"><div class="num-tag-label">ЛЕГЕНДАРНЫХ</div><div class="num-tag-value" id="statLegendary">0</div></div>
        <div class="num-tag-box"><div class="num-tag-label">В КОЛЛЕКЦИИ</div><div class="num-tag-value" id="statCollection">0</div></div>
    </div>
</div>

<!-- МАГАЗИН -->
<div class="screen" id="screen-shop">
    <div class="section-header">🛒 Магазин</div>
    <div class="now-item" onclick="showAlert('Скоро', 'Магазин в разработке')">
        <div class="now-icon gi-yellow">👑</div>
        <div class="now-title">VIP-статус</div>
        <div class="now-value gold">100 000 ₽</div>
    </div>
    <div class="now-item" onclick="showAlert('Скоро', 'Магазин в разработке')">
        <div class="now-icon gi-purple">🍀</div>
        <div class="now-title">Удача ×5</div>
        <div class="now-value purple">50 000 ₽</div>
    </div>
    <div class="now-item" onclick="showAlert('Скоро', 'Магазин в разработке')">
        <div class="now-icon gi-blue">🎨</div>
        <div class="now-title">Оформление</div>
        <div class="now-value blue">25 000 ₽</div>
    </div>
</div>

<!-- ДРУЗЬЯ -->
<div class="screen" id="screen-friends">
    <div class="section-header">👥 Друзья</div>
    <div class="now-item">
        <div class="now-icon gi-blue">✈️</div>
        <div class="now-title">Пригласить друга</div>
        <div class="now-value green">+1 200 ₽</div>
    </div>
    <div class="empty"><div class="empty-icon">👥</div>Пока нет друзей</div>
</div>

<!-- ПРОМОКОД -->
<div class="screen" id="screen-promo">
    <div class="section-header">🎟️ Промокод</div>
    <input type="text" id="promoInput" placeholder="ВВЕДИ КОД" style="width:100%;padding:16px;border-radius:14px;border:2px solid rgba(255,255,255,0.1);background:rgba(0,0,0,0.4);color:#fff;font-size:15px;font-weight:900;text-transform:uppercase;text-align:center;letter-spacing:2px;margin-bottom:10px;">
    <button class="btn-spin" onclick="applyPromo()" style="padding:16px;">АКТИВИРОВАТЬ</button>
</div>

<!-- ИСТОРИЯ -->
<div class="screen" id="screen-history">
    <div class="section-header">📜 История</div>
    <div id="historyList"></div>
</div>

<!-- ПРОСТЫЕ ЭКРАНЫ -->
<div class="screen" id="screen-upgrader"><div class="section-header">⬆️ Апгрейдер</div><div class="empty"><div class="empty-icon">⬆️</div>Скоро</div></div>
<div class="screen" id="screen-skills"><div class="section-header">✨ Навыки</div><div class="empty"><div class="empty-icon">✨</div>Скоро</div></div>
<div class="screen" id="screen-trading"><div class="section-header">🏪 Торговля</div><div class="empty"><div class="empty-icon">🏪</div>Скоро</div></div>
<div class="screen" id="screen-achievements"><div class="section-header">⭐ Достижения</div><div class="empty"><div class="empty-icon">⭐</div>Скоро</div></div>
<div class="screen" id="screen-deals"><div class="section-header">🔀 Сделки</div><div class="empty"><div class="empty-icon">🔀</div>Скоро</div></div>
<div class="screen" id="screen-phones"><div class="section-header">📱 Телефоны</div><div class="empty"><div class="empty-icon">📱</div>Скоро</div></div>
<div class="screen" id="screen-number_check"><div class="section-header">🔍 Проверка номера</div><div class="empty"><div class="empty-icon">🔍</div>Скоро</div></div>
<div class="screen" id="screen-salons"><div class="section-header">📡 Салоны</div><div class="empty"><div class="empty-icon">📡</div>Скоро</div></div>
<div class="screen" id="screen-chances"><div class="section-header">% Шансы</div><div class="empty"><div class="empty-icon">%</div>Скоро</div></div>
<div class="screen" id="screen-rules"><div class="section-header">ℹ️ Правила</div><div class="empty"><div class="empty-icon">ℹ️</div>Игра про номера</div></div>
<div class="screen" id="screen-settings"><div class="section-header">⚙️ Настройки</div><div class="empty"><div class="empty-icon">⚙️</div>Скоро</div></div>

<div class="side-menu" id="sideMenu">
    <div class="menu-close"><span>Меню</span><span onclick="toggleMenu()">✕</span></div>
    <div class="menu-item" onclick="switchScreen('main');toggleMenu()">🏠 Главная</div>
    <div class="menu-item" onclick="switchScreen('rating');toggleMenu()">🏆 Рейтинг</div>
    <div class="menu-item" onclick="switchScreen('profile');toggleMenu()">👤 Профиль</div>
    <div class="menu-item" onclick="switchScreen('shop');toggleMenu()">🛒 Магазин</div>
    <div class="menu-item" onclick="switchScreen('settings');toggleMenu()">⚙️ Настройки</div>
</div>
<div class="overlay-bg" id="overlayBg" onclick="toggleMenu()"></div>

<script>
// ============================================================
// ⚙️ НАСТРОЙКА
// ============================================================
const JSONBIN_ID = "";   // ← Bin ID с jsonbin.io (для общего топа)
const JSONBIN_KEY = "";  // ← Master Key
// ============================================================

let tg = null;
try { tg = window.Telegram?.WebApp; } catch(e) {}
if (tg) { tg.ready(); tg.expand(); }

function $(id) { return document.getElementById(id); }
function num(v) { v = Number(v); return (!isFinite(v) || isNaN(v)) ? 0 : v; }
function fmt(n) { n = num(n); if (n >= 1e9) return (n/1e9).toFixed(2)+' млрд'; if (n >= 1e6) return (n/1e6).toFixed(2)+' млн'; if (n >= 1e3) return (n/1e3).toFixed(1)+'к'; return Math.floor(n).toString(); }
function fmtFull(n) { return Math.floor(num(n)).toLocaleString('ru-RU'); }

function defaultState() {
    return {
        money: 164, level: 15, xp: 5525, totalSpins: 0, totalEarned: 0,
        legendaryCount: 0, epicCount: 0, rareCount: 0,
        collection: [], currentNum: null, bestNum: null, history: [],
        userName: 'Гость', userId: null, userPhoto: null,
        dailyStreak: 0, lastDaily: 0,
        usedPromos: [],
        luckMultiplier: 1, luckSpinsLeft: 0,
        rouletteFreeUsed: 0,
        seasonScore: 0
    };
}

let state = defaultState();
try {
    let saved = localStorage.getItem('cifrki_v2');
    if (saved) {
        let p = JSON.parse(saved);
        for (let k in state) if (p[k] !== undefined && p[k] !== null) state[k] = p[k];
        if (!Array.isArray(state.collection)) state.collection = [];
        if (!Array.isArray(state.history)) state.history = [];
        if (!Array.isArray(state.usedPromos)) state.usedPromos = [];
    }
} catch(e) {}

// Авто-вход через Telegram
if (tg && tg.initDataUnsafe && tg.initDataUnsafe.user) {
    let u = tg.initDataUnsafe.user;
    state.userName = (u.first_name || '') + (u.last_name ? ' ' + u.last_name : '');
    state.userId = u.id;
    if (u.photo_url) state.userPhoto = u.photo_url;
}

function save() { try { localStorage.setItem('cifrki_v2', JSON.stringify(state)); } catch(e) {} }

function xpForLevel(level) { return Math.floor(1000 * Math.pow(1.5, level - 1)); }

function addXP(amount) {
    state.xp += num(amount);
    let leveled = false;
    while (state.xp >= xpForLevel(state.level)) {
        state.xp -= xpForLevel(state.level);
        state.level++;
        state.money += 500 * state.level;
        leveled = true;
        showFloating('⭐ Ур. ' + state.level + '!');
    }
    if (leveled) save();
    updateUI();
}

// === ОПЕРАТОРЫ ===
const OPERATORS = [
    { name: 'МТС', color: '#e60000', prefix: '917' },
    { name: 'Билайн', color: '#ffcc00', prefix: '962' },
    { name: 'Мегафон', color: '#00b300', prefix: '925' },
    { name: 'Tele2', color: '#000000', prefix: '900' },
    { name: 'Yota', color: '#00aaff', prefix: '999' },
    { name: 'Тинькофф', color: '#ffdd2d', prefix: '988' },
    { name: 'СберМобайл', color: '#21a038', prefix: '958' }
];

// === ОЦЕНКА ===
function evaluateNumber(allDigits) {
    let score = 0, reasons = [];
    let counts = {};
    for (let i = 0; i < allDigits.length; i++) counts[allDigits[i]] = (counts[allDigits[i]] || 0) + 1;
    let maxRepeat = 0;
    for (let k in counts) if (counts[k] > maxRepeat) maxRepeat = counts[k];

    if (maxRepeat === 7) { score += 100000; reasons.push("🔥 Все 7 одинаковые"); }
    else if (maxRepeat === 6) { score += 30000; reasons.push("💎 6 одинаковых"); }
    else if (maxRepeat === 5) { score += 8000; reasons.push("⭐ 5 одинаковых"); }
    else if (maxRepeat === 4) { score += 1500; reasons.push("4 одинаковые"); }
    else if (maxRepeat === 3) { score += 250; reasons.push("3 одинаковые"); }
    else if (maxRepeat === 2) { score += 20; reasons.push("2 одинаковые"); }

    let maxSeq = 1;
    for (let i = 0; i < allDigits.length - 1; i++) {
        let dir = parseInt(allDigits[i+1]) - parseInt(allDigits[i]);
        if (Math.abs(dir) !== 1) continue;
        let seq = 2;
        for (let j = i + 2; j < allDigits.length; j++) {
            if (parseInt(allDigits[j]) - parseInt(allDigits[j-1]) === dir) seq++;
            else break;
        }
        if (seq > maxSeq) maxSeq = seq;
    }
    if (maxSeq === 7) { score += 50000; reasons.push("🔥 Последовательность"); }
    else if (maxSeq === 6) { score += 15000; reasons.push("💎 6 подряд"); }
    else if (maxSeq === 5) { score += 3500; reasons.push("⭐ 5 подряд"); }
    else if (maxSeq === 4) { score += 600; reasons.push("4 подряд"); }
    else if (maxSeq === 3) { score += 80; reasons.push("3 подряд"); }

    if (allDigits === allDigits.split('').reverse().join('')) { score += 6000; reasons.push("🪞 Палиндром"); }
    if (allDigits.slice(-3) === '000') { score += 1500; reasons.push("000 в конце"); }
    if (allDigits.slice(-3) === '777') { score += 1200; reasons.push("777 в конце"); }

    let pairs = 0;
    for (let i = 0; i < allDigits.length - 1; i += 2) if (allDigits[i] === allDigits[i+1]) pairs++;
    if (pairs >= 3) { score += 2500; reasons.push("3 зеркальные пары"); }
    else if (pairs === 2) { score += 400; reasons.push("2 зеркальные пары"); }

    let rarity, color, price;
    if (score >= 40000) {
        rarity = "🔥 Легендарный"; color = "linear-gradient(135deg, #FFD700, #FF8C00)";
        let prog = Math.min((score - 40000) / 60000, 1);
        price = 3000000 + prog * 97000000;
    } else if (score >= 12000) {
        rarity = "🟣 Эпический"; color = "linear-gradient(135deg, #9C27B0, #6A1B9A)";
        let prog = Math.min((score - 12000) / 28000, 1);
        price = 120000 + prog * 2880000;
    } else if (score >= 3000) {
        rarity = "🔵 Редкий"; color = "linear-gradient(135deg, #2196F3, #1565C0)";
        price = 15000 + ((score - 3000) / 9000) * 105000;
    } else if (score >= 1000) {
        rarity = "🟢 Зеркальный"; color = "linear-gradient(135deg, #4CAF50, #2E7D32)";
        price = 3000 + (score - 1000) * 5;
    } else if (score >= 250) {
        rarity = "🟡 Необычный"; color = "linear-gradient(135deg, #FFC107, #FF8F00)";
        price = 600 + (score - 250) * 8;
    } else {
        rarity = "⚪ Обычный"; color = "#666";
        price = 200 + score * 1.5;
    }
    price = Math.floor(price * (0.85 + Math.random() * 0.3));
    if (reasons.length === 0) reasons.push("Обычный номер");
    return { rarity, color, price, reasons: reasons.slice(0, 2), score };
}

function formatDisplay(d) {
    let s = "";
    for (let i = 0; i < d.length; i++) {
        s += d[i];
        if (i === 2 || i === 4) s += "-";
    }
    return s;
}

// === ГЕНЕРАЦИЯ ===
let isSpinning = false;
function generateNumber() {
    if (isSpinning) return;
    if (state.currentNum) keepNumber();
    if (state.money < 500) { showFloating('❌ Мало денег'); return; }

    isSpinning = true;
    state.money -= 500;
    state.totalSpins++;
    updateUI();

    let op = OPERATORS[Math.floor(Math.random() * OPERATORS.length)];
    let digits = "";
    for (let i = 0; i < 7; i++) digits += Math.floor(Math.random() * 10);
    let evalRes = evaluateNumber(op.prefix + digits);

    // Создаём окно генерации
    showGenerator(op, digits, evalRes);
}

function showGenerator(op, digits, evalRes) {
    // Модальное окно с анимацией
    let modal = document.createElement('div');
    modal.id = 'genModal';
    modal.style.cssText = 'position:fixed;top:0;left:0;right:0;bottom:0;background:rgba(0,0,0,0.9);z-index:500;display:flex;align-items:center;justify-content:center;padding:20px;';
    modal.innerHTML = `
        <div class="num-card" id="genCard" style="width:100%;max-width:400px;">
            <div class="num-top">
                <div class="num-op">
                    <div class="num-op-logo" style="background:${op.color}">${op.name[0]}</div>
                    <div class="num-op-name">${op.name}</div>
                </div>
                <div class="num-sim"></div>
            </div>
            <div class="num-value spinning" id="genNumDisplay">+7 --- --- -- --</div>
            <div class="num-tags-row">
                <div class="num-tag-box"><div class="num-tag-label">РЕГИОН</div><div class="num-tag-value">${['Москва','СПб','Регион','Премиум'][Math.floor(Math.random()*4)]}</div></div>
                <div class="num-tag-box"><div class="num-tag-label">РЕДКОСТЬ</div><div class="num-tag-value" id="genRarityText">...</div></div>
            </div>
            <div class="num-rarity-wrap">
                <div class="num-rarity-badge" id="genRarityBadge" style="background:#666;">...</div>
                <div class="num-price-display" id="genPriceDisplay">...</div>
                <div class="num-reason" id="genReason"></div>
            </div>
        </div>
    `;
    document.body.appendChild(modal);

    let isLegend = evalRes.score >= 40000;
    let isEpic = evalRes.score >= 12000;
    let speed = isLegend ? 50 : isEpic ? 40 : 30;
    let duration = isLegend ? 1500 : isEpic ? 1100 : 700;

    let start = Date.now();
    let anim = setInterval(function() {
        if (Date.now() - start < duration * 0.6) {
            let t = "";
            for (let i = 0; i < 7; i++) t += Math.floor(Math.random() * 10);
            $('genNumDisplay').innerText = '+7 ' + op.prefix + ' ' + formatDisplay(t);
        } else {
            clearInterval(anim);
            let displayed = "";
            let i = 0;
            let stop = setInterval(function() {
                if (i >= 7) {
                    clearInterval(stop);
                    $('genNumDisplay').classList.remove('spinning');
                    finishGeneration(op, digits, evalRes, modal);
                    return;
                }
                displayed += digits[i];
                let tail = "";
                for (let k = i + 1; k < 7; k++) tail += "•";
                $('genNumDisplay').innerText = '+7 ' + op.prefix + ' ' + formatDisplay(displayed + tail);
                i++;
            }, isEpic || isLegend ? 90 : 55);
        }
    }, speed);
}

function finishGeneration(op, digits, evalRes, modal) {
    $('genNumDisplay').innerText = '+7 ' + op.prefix + ' ' + formatDisplay(digits);

    let isLegend = evalRes.rarity.indexOf('Легендарный') !== -1;
    let isEpic = evalRes.rarity.indexOf('Эпический') !== -1;
    let isRare = evalRes.rarity.indexOf('Редкий') !== -1;

    let finalPrice = Math.floor(evalRes.price * (1 + state.level * 0.1) * state.luckMultiplier);
    if (isLegend && finalPrice < 3000000) finalPrice = 3000000;
    if (isEpic && finalPrice < 120000) finalPrice = 120000;

    state.currentNum = {
        number: '+7 ' + op.prefix + ' ' + formatDisplay(digits),
        operator: op, rarity: evalRes.rarity, color: evalRes.color,
        price: finalPrice,
        region: $('genCard').querySelector('.num-tag-value').innerText
    };

    if (isLegend) state.legendaryCount++;
    else if (isEpic) state.epicCount++;
    else if (isRare) state.rareCount++;

    if (!state.bestNum || finalPrice > state.bestNum.price) {
        state.bestNum = JSON.parse(JSON.stringify(state.currentNum));
    }

    state.seasonScore += finalPrice;

    if (state.luckSpinsLeft > 0) {
        state.luckSpinsLeft--;
        if (state.luckSpinsLeft === 0) state.luckMultiplier = 1;
    }

    $('genRarityText').innerText = evalRes.rarity;
    $('genRarityBadge').innerText = evalRes.rarity;
    $('genRarityBadge').style.background = evalRes.color;
    $('genPriceDisplay').innerText = fmtFull(finalPrice) + ' ₽';
    $('genReason').innerText = evalRes.reasons[0] || '';

    if (isLegend) $('genCard').classList.add('legendary');
    else if (isEpic) $('genCard').classList.add('epic');
    else if (isRare) $('genCard').classList.add('rare');

    // Кнопки
    let actions = document.createElement('div');
    actions.className = 'actions';
    actions.style.marginTop = '16px';
    actions.innerHTML = `
        <button class="btn-sell" onclick="sellNumberFromModal()">💸 ПРОДАТЬ</button>
        <button class="btn-keep" onclick="keepNumberFromModal()">📦 В КОЛЛЕКЦИЮ</button>
    `;
    $('genCard').appendChild(actions);

    if (isLegend && navigator.vibrate) navigator.vibrate([100,50,100,50,200]);
    else if (isEpic && navigator.vibrate) navigator.vibrate([80,40,80]);

    // История
    state.history.unshift({
        number: state.currentNum.number,
        rarity: state.currentNum.rarity,
        price: finalPrice,
        date: Date.now()
    });
    if (state.history.length > 50) state.history.pop();

    save(); updateUI();
    isSpinning = false;
}

function sellNumberFromModal() {
    if (!state.currentNum) return;
    let p = state.currentNum.price;
    state.money += p;
    state.totalEarned += p;
    addXP(Math.floor(p / 1000));
    showFloating('+ ' + fmtFull(p) + ' ₽');
    state.currentNum = null;
    closeModal();
    save(); updateUI();
}

function keepNumberFromModal() {
    if (!state.currentNum) return;
    state.collection.push(state.currentNum);
    showFloating('📦 В коллекции!');
    state.currentNum = null;
    closeModal();
    save(); updateUI();
}

function closeModal() {
    let m = $('genModal');
    if (m) m.remove();
    let card = $('numberCard');
    if (card) card.style.display = 'none';
    let actions = $('actionBtns');
    if (actions) actions.style.display = 'none';
    let spinBtn = $('spinBtn');
    if (spinBtn) { spinBtn.disabled = false; spinBtn.innerText = '📞 ВЫБИТЬ НОМЕР (500 ₽)'; }
}

function sellNumber() { sellNumberFromModal(); }
function keepNumber() { keepNumberFromModal(); }

// === РУЛЕТКА УДАЧИ ===
function playRoulette() {
    if (state.rouletteFreeUsed >= 2 && state.money < 5000) {
        showAlert('Рулетка', 'Бесплатные попытки закончились. Стоимость: 5 000 ₽');
        return;
    }
    let cost = state.rouletteFreeUsed >= 2 ? 5000 : 0;
    if (cost > 0) state.money -= cost;
    else state.rouletteFreeUsed++;

    let outcomes = [2, 3, 5, 10];
    let weights = [50, 30, 15, 5];
    let total = 100, rand = Math.random() * total, chosen = 2;
    for (let i = 0; i < outcomes.length; i++) {
        if (rand < weights[i]) { chosen = outcomes[i]; break; }
        rand -= weights[i];
    }
    state.luckMultiplier = chosen;
    state.luckSpinsLeft = 3;
    showFloating('🎰 ×' + chosen + ' на 3 номера!');
    save(); updateUI();
}

// === НАГРАДА ДНЯ ===
function canClaimDaily() { return (Date.now() - num(state.lastDaily)) >= 86400000; }

function claimDaily() {
    if (!canClaimDaily()) {
        let left = 86400000 - (Date.now() - state.lastDaily);
        let h = Math.floor(left / 3600000);
        let m = Math.floor((left % 3600000) / 60000);
        showAlert('Награда дня', 'Уже получена. Ждать: ' + h + 'ч ' + m + 'м');
        return;
    }
    let amount = 10000 + state.dailyStreak * 5000;
    state.dailyStreak++;
    state.lastDaily = Date.now();
    state.money += amount;
    addXP(100);
    showFloating('🎁 +' + fmtFull(amount) + ' ₽');
    save(); updateUI();
}

// === БОНУС ===
function claimBonus() {
    let amount = 50000;
    state.money += amount;
    addXP(200);
    showFloating('⏱️ +' + fmtFull(amount) + ' ₽');
    save(); updateUI();
}

// === СКУПЩИК ===
function buyerOffer() {
    if (!state.currentNum) { showAlert('Скупщик', 'Сначала выбей номер'); return; }
    let mult = 0.7 + Math.random() * 0.8;
    let offer = Math.floor(state.currentNum.price * mult);
    let text = 'Скупщик предлагает ' + fmtFull(offer) + ' ₽ за номер.\n\n' + (mult > 1.1 ? '🔥 Это выгодно!' : mult < 0.9 ? '📉 Лучше продай сам' : '🤝 Обычная цена');
    if (confirm(text + '\n\nСогласен?')) {
        state.money += offer;
        state.totalEarned += offer;
        state.currentNum = null;
        closeModal();
        addXP(Math.floor(offer / 1000));
        save(); updateUI();
    }
}

// === ПРОМОКОД ===
function applyPromo() {
    let code = $('promoInput').value.trim().toUpperCase();
    if (code === '') return;
    let codes = { 'START': 5000, 'TIKTOK': 10000, 'GOLD': 25000, 'VIP': 50000 };
    if (!codes[code]) { showAlert('❌', 'Промокод не найден'); return; }
    if (state.usedPromos.includes(code)) { showAlert('❌', 'Уже использован'); return; }
    state.usedPromos.push(code);
    state.money += codes[code];
    addXP(50);
    $('promoInput').value = '';
    showFloating('🎟️ +' + fmtFull(codes[code]) + ' ₽');
    save(); updateUI();
}

// === РЕЙТИНГ ===
let currentFilter = 'season';

function setFilter(f) {
    currentFilter = f;
    document.querySelectorAll('.filter-tab').forEach(t => t.classList.toggle('active', t.dataset.filter === f));
    renderRating();
}

function renderRating() {
    let myValue = 0;
    let myLabel = '';
    if (currentFilter === 'season') { myValue = state.seasonScore; myLabel = fmt(myValue) + ' ₽'; }
    if (currentFilter === 'best') { myValue = state.bestNum ? state.bestNum.price : 0; myLabel = fmt(myValue) + ' ₽'; }
    if (currentFilter === 'rich') { myValue = state.money + state.totalEarned; myLabel = fmt(myValue) + ' ₽'; }
    if (currentFilter === 'invites') { myValue = 0; myLabel = '0'; }
    if (currentFilter === 'level') { myValue = state.level; myLabel = 'Ур. ' + myValue; }

    $('myRankValue').innerText = myLabel;

    // Локальный топ (показываем себя + ботов для вида)
    let bots = [
        { name: 'salem', value: 36370000000, vip: true },
        { name: 'GG', value: 8610000000, vip: true },
        { name: 'зелёный', value: 7270000000, vip: false },
        { name: 'дунай', value: 5110000000, vip: true },
        { name: 'Игрок', value: 4800000000, vip: true },
        { name: 'Хаос', value: 3200000000, vip: false },
        { name: 'Максим', value: 2100000000, vip: true },
        { name: 'Анна', value: 1500000000, vip: false }
    ];

    let all = [...bots];
    all.push({ name: state.userName || 'Вы', value: myValue, me: true, vip: false, photo: state.userPhoto });
    all.sort((a, b) => b.value - a.value);

    let html = '';
    all.forEach((p, i) => {
        let rankClass = i === 0 ? 'top1' : i === 1 ? 'top2' : i === 2 ? 'top3' : '';
        let avatarHtml = p.photo ? '<img src="' + p.photo + '">' : (p.me ? '👤' : '🎮');
        let valueText = currentFilter === 'level' ? 'Ур. ' + p.value : fmt(p.value) + ' ₽';
        html += '<div class="top-item' + (p.me ? ' me' : '') + '">' +
            '<div class="top-item-rank ' + rankClass + '">' + (i + 1) + '</div>' +
            '<div class="top-item-avatar">' + avatarHtml + '</div>' +
            '<div class="top-item-name">' + p.name + (p.vip ? ' <span class="top-item-vip">VIP</span>' : '') + '</div>' +
            '<div class="top-item-value">' + valueText + '</div>' +
        '</div>';
        if (p.me) $('myRankNum').innerText = i + 1;
    });
    $('topList').innerHTML = html;
}

// === ИСТОРИЯ ===
function renderHistory() {
    let el = $('historyList');
    if (state.history.length === 0) {
        el.innerHTML = '<div class="empty"><div class="empty-icon">📜</div>История пуста</div>';
        return;
    }
    let html = '';
    state.history.forEach(h => {
        let cls = '';
        if (h.rarity.indexOf('Легендарный') !== -1) cls = 'legendary';
        else if (h.rarity.indexOf('Эпический') !== -1) cls = 'epic';
        else if (h.rarity.indexOf('Редкий') !== -1) cls = 'rare';
        html += '<div class="coll-item ' + cls + '">' +
            '<div><div class="coll-num">' + h.number + '</div><div class="coll-rarity">' + h.rarity + '</div></div>' +
            '<div class="coll-price">' + fmtFull(h.price) + ' ₽</div></div>';
    });
    el.innerHTML = html;
}

// === КОЛЛЕКЦИЯ ===
function renderCollection() {
    let el = $('collectionList');
    if (state.collection.length === 0) {
        el.innerHTML = '<div class="empty"><div class="empty-icon">📦</div>Коллекция пуста<br><small>Выбей номер и оставь в коллекцию</small></div>';
        return;
    }
    let html = '';
    [...state.collection].reverse().forEach((c, idx) => {
        let cls = '';
        if (c.rarity.indexOf('Легендарный') !== -1) cls = 'legendary';
        else if (c.rarity.indexOf('Эпический') !== -1) cls = 'epic';
        else if (c.rarity.indexOf('Редкий') !== -1) cls = 'rare';
        html += '<div class="coll-item ' + cls + '">' +
            '<div><div class="coll-num">' + c.number + '</div><div class="coll-rarity">' + c.rarity + '</div></div>' +
            '<div class="coll-price">' + fmtFull(c.price) + ' ₽</div></div>';
    });
    el.innerHTML = html;
}

// === UI ===
function updateUI() {
    $('headerBalance').innerText = fmtFull(state.money) + ' ₽';
    $('headerLevel').innerText = state.level;
    $('headerXpFill').style.width = Math.min(100, (state.xp / xpForLevel(state.level)) * 100) + '%';
    $('passLevel').innerText = Math.min(99, state.level - 15);
    $('dailyStreak').innerText = state.dailyStreak;
    $('buyerMultiplier').innerText = 'до ×' + (2 + Math.random() * 2).toFixed(2);
    $('luckDisplay').innerText = state.luckMultiplier > 1 ? '×' + state.luckMultiplier + ' (осталось ' + state.luckSpinsLeft + ')' : '×1';
    $('rouletteFree').innerText = state.rouletteFreeUsed + ' из 2 бесплатно';
    $('levelValue').innerText = state.level + ' · ещё ' + (xpForLevel(state.level) - state.xp) + ' XP';

    // Аватарки
    let avatarHtml = state.userPhoto ? '<img src="' + state.userPhoto + '">' : '👤';
    $('userAvatar').innerHTML = avatarHtml;
    $('profileAvatar').innerHTML = avatarHtml;
    $('myRankAvatar').innerHTML = avatarHtml;

    $('profileName').innerText = state.userName;
    $('profileId').innerText = 'ID: ' + (state.userId || '---');

    $('statSpins').innerText = state.totalSpins;
    $('statEarned').innerText = fmt(state.totalEarned) + ' ₽';
    $('statLegendary').innerText = state.legendaryCount;
    $('statCollection').innerText = state.collection.length;

    // Ежедневный бейдж
    let badge = $('dailyBadge');
    if (badge) badge.style.display = canClaimDaily() ? 'block' : 'none';

    // Кнопки в главном экране
    let spinBtn = $('spinBtn');
    if (spinBtn) spinBtn.innerText = '📞 ВЫБИТЬ НОМЕР (500 ₽)';

    renderCollection();
    renderHistory();
    if ($('screen-rating').classList.contains('active')) renderRating();
}

function showFloating(text) {
    let el = document.createElement('div');
    el.className = 'floating';
    el.innerText = text;
    document.body.appendChild(el);
    setTimeout(() => el.remove(), 2000);
}

function showAlert(title, text) {
    alert(title + '\n\n' + text);
}

let history = ['main'];
function switchScreen(name) {
    if (history[history.length - 1] !== name) { history.push(name); if (history.length > 15) history.shift(); }
    document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
    let el = $('screen-' + name);
    if (el) el.classList.add('active');
    $('backBtn').classList.toggle('show', name !== 'main');
    $('sideMenu').classList.remove('open');
    $('overlayBg').classList.remove('show');

    if (name === 'rating') renderRating();
    if (name === 'collection') renderCollection();
    if (name === 'history') renderHistory();
    window.scrollTo(0, 0);
}

function goBack() {
    if (history.length > 1) {
        history.pop();
        let prev = history[history.length - 1];
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        let el = $('screen-' + prev);
        if (el) el.classList.add('active');
        $('backBtn').classList.toggle('show', prev !== 'main');
        if (prev === 'rating') renderRating();
        if (prev === 'collection') renderCollection();
        if (prev === 'history') renderHistory();
    } else switchScreen('main');
}

function toggleMenu() {
    let m = $('sideMenu'), o = $('overlayBg');
    if (m.classList.contains('open')) { m.classList.remove('open'); o.classList.remove('show'); }
    else { m.classList.add('open'); o.classList.add('show'); }
}

// Добавляем кнопку "Выбить номер" в главный экран
let mainScreen = $('screen-main');
let spinButton = document.createElement('button');
spinButton.className = 'btn-spin';
spinButton.id = 'spinBtn';
spinButton.innerText = '📞 ВЫБИТЬ НОМЕР (500 ₽)';
spinButton.onclick = generateNumber;
mainScreen.appendChild(spinButton);

updateUI();
console.log('📱 Цифрки загружены!', tg ? 'Telegram активен' : 'Без Telegram');
</script>
</body>
</html>
