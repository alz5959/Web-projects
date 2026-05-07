<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chat Simulation</title>
    <style>
        @import url('https://fontsource.org/fonts/inter/400.css');
        @import url('https://fontsource.org/fonts/inter/500.css');
        @import url('https://fontsource.org/fonts/inter/600.css');

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', sans-serif;
            min-height: 100vh;
            background: linear-gradient(135deg, #0a1628 0%, #0d2137 25%, #0a2a2a 50%, #0d1f3c 75%, #0a1628 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
            overflow: hidden;
        }

        .bg-orbs {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 0;
        }

        .bg-orb {
            position: absolute;
            border-radius: 50%;
            filter: blur(80px);
            animation: orbFloat 20s ease-in-out infinite;
        }

        .bg-orb:nth-child(1) {
            width: 400px;
            height: 400px;
            background: radial-gradient(circle, rgba(0, 200, 150, 0.15), transparent);
            top: -100px;
            left: -100px;
            animation-delay: 0s;
        }

        .bg-orb:nth-child(2) {
            width: 500px;
            height: 500px;
            background: radial-gradient(circle, rgba(0, 150, 255, 0.12), transparent);
            bottom: -150px;
            right: -150px;
            animation-delay: -7s;
        }

        .bg-orb:nth-child(3) {
            width: 350px;
            height: 350px;
            background: radial-gradient(circle, rgba(0, 230, 180, 0.1), transparent);
            top: 50%;
            left: 60%;
            animation-delay: -14s;
        }

        @keyframes orbFloat {
            0%, 100% { transform: translate(0, 0) scale(1); }
            25% { transform: translate(50px, -30px) scale(1.1); }
            50% { transform: translate(-30px, 50px) scale(0.95); }
            75% { transform: translate(40px, 20px) scale(1.05); }
        }

        .chat-container {
            position: relative;
            z-index: 1;
            width: 100%;
            max-width: 480px;
            height: 90vh;
            max-height: 750px;
            background: rgba(255, 255, 255, 0.04);
            backdrop-filter: blur(40px);
            -webkit-backdrop-filter: blur(40px);
            border-radius: 28px;
            border: 1px solid rgba(255, 255, 255, 0.08);
            box-shadow: 
                0 8px 32px rgba(0, 0, 0, 0.3),
                inset 0 1px 0 rgba(255, 255, 255, 0.1),
                inset 0 -1px 0 rgba(0, 0, 0, 0.1);
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .chat-header {
            padding: 20px 24px;
            background: rgba(255, 255, 255, 0.03);
            border-bottom: 1px solid rgba(255, 255, 255, 0.06);
            display: flex;
            align-items: center;
            gap: 14px;
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
        }

        .header-avatar {
            width: 44px;
            height: 44px;
            border-radius: 12px;
            object-fit: cover;
            border: 2px solid rgba(0, 200, 150, 0.3);
            box-shadow: 0 0 15px rgba(0, 200, 150, 0.15);
        }

        .header-info {
            flex: 1;
        }

        .header-title {
            color: #e8f5f0;
            font-size: 16px;
            font-weight: 600;
            letter-spacing: 0.01em;
        }

        .header-subtitle {
            color: rgba(0, 200, 150, 0.7);
            font-size: 12px;
            font-weight: 500;
            display: flex;
            align-items: center;
            gap: 5px;
            margin-top: 2px;
        }

        .online-dot {
            width: 7px;
            height: 7px;
            background: #00c896;
            border-radius: 50%;
            display: inline-block;
            animation: pulse 2s ease-in-out infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; box-shadow: 0 0 0 0 rgba(0, 200, 150, 0.4); }
            50% { opacity: 0.8; box-shadow: 0 0 0 6px rgba(0, 200, 150, 0); }
        }

        .header-actions {
            display: flex;
            gap: 8px;
        }

        .header-btn {
            width: 38px;
            height: 38px;
            border-radius: 12px;
            background: rgba(255, 255, 255, 0.06);
            border: 1px solid rgba(255, 255, 255, 0.08);
            color: rgba(200, 230, 220, 0.7);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .header-btn:hover {
            background: rgba(0, 200, 150, 0.12);
            border-color: rgba(0, 200, 150, 0.2);
            color: #00c896;
        }

        .chat-messages {
            flex: 1;
            overflow-y: auto;
            padding: 20px 16px;
            display: flex;
            flex-direction: column;
            gap: 16px;
            scroll-behavior: smooth;
        }

        .chat-messages::-webkit-scrollbar {
            width: 4px;
        }

        .chat-messages::-webkit-scrollbar-track {
            background: transparent;
        }

        .chat-messages::-webkit-scrollbar-thumb {
            background: rgba(0, 200, 150, 0.15);
            border-radius: 4px;
        }

        .message-row {
            display: flex;
            gap: 10px;
            animation: messageIn 0.5s cubic-bezier(0.16, 1, 0.3, 1) forwards;
            opacity: 0;
            transform: translateY(20px);
        }

        .message-row.sent {
            flex-direction: row-reverse;
        }

        @keyframes messageIn {
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .avatar {
            width: 40px;
            height: 40px;
            border-radius: 12px;
            object-fit: cover;
            flex-shrink: 0;
            border: 2px solid rgba(255, 255, 255, 0.08);
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
            transition: transform 0.3s ease;
        }

        .avatar:hover {
            transform: scale(1.08);
        }

        .message-bubble {
            max-width: 75%;
            padding: 14px 18px;
            border-radius: 20px;
            position: relative;
            line-height: 1.5;
            font-size: 14px;
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            transition: transform 0.2s ease;
        }

        .message-bubble:hover {
            transform: scale(1.01);
        }

        .message-row.received .message-bubble {
            background: rgba(255, 255, 255, 0.06);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-bottom-left-radius: 6px;
            color: #d4ece4;
            box-shadow: 
                0 4px 16px rgba(0, 0, 0, 0.15),
                inset 0 1px 0 rgba(255, 255, 255, 0.06);
        }

        .message-row.sent .message-bubble {
            background: linear-gradient(135deg, rgba(0, 180, 130, 0.2), rgba(0, 140, 200, 0.15));
            border: 1px solid rgba(0, 200, 150, 0.15);
            border-bottom-right-radius: 6px;
            color: #d4ece4;
            box-shadow: 
                0 4px 16px rgba(0, 180, 130, 0.1),
                inset 0 1px 0 rgba(255, 255, 255, 0.08);
        }

        .sender-name {
            font-size: 12px;
            font-weight: 600;
            margin-bottom: 4px;
            color: rgba(0, 200, 150, 0.8);
        }

        .message-text {
            word-wrap: break-word;
        }

        .message-time {
            font-size: 10px;
            color: rgba(200, 220, 210, 0.35);
            margin-top: 6px;
            text-align: right;
        }

        .video-message {
            width: 240px;
            border-radius: 16px;
            overflow: hidden;
            position: relative;
            cursor: pointer;
            transition: transform 0.3s ease;
        }

        .video-message:hover {
            transform: scale(1.02);
        }

        .video-thumb {
            width: 100%;
            height: 140px;
            object-fit: cover;
            display: block;
        }

        .video-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 140px;
            background: rgba(0, 0, 0, 0.25);
            display: flex;
            align-items: center;
            justify-content: center;
            transition: background 0.3s ease;
        }

        .video-message:hover .video-overlay {
            background: rgba(0, 0, 0, 0.35);
        }

        .play-btn {
            width: 48px;
            height: 48px;
            background: rgba(0, 200, 150, 0.8);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 4px 20px rgba(0, 200, 150, 0.3);
            transition: all 0.3s ease;
        }

        .video-message:hover .play-btn {
            transform: scale(1.1);
            box-shadow: 0 4px 30px rgba(0, 200, 150, 0.5);
        }

        .play-btn svg {
            margin-left: 3px;
        }

        .video-duration {
            position: absolute;
            bottom: 8px;
            right: 8px;
            background: rgba(0, 0, 0, 0.6);
            color: white;
            font-size: 11px;
            padding: 2px 8px;
            border-radius: 6px;
            font-weight: 500;
        }

        .video-info {
            padding: 10px 14px;
            background: rgba(0, 0, 0, 0.2);
            backdrop-filter: blur(10px);
        }

        .video-sender {
            font-size: 12px;
            color: rgba(0, 200, 150, 0.8);
            font-weight: 600;
        }

        .video-caption {
            font-size: 12px;
            color: rgba(200, 220, 210, 0.6);
            margin-top: 2px;
        }

        .date-separator {
            text-align: center;
            margin: 8px 0;
            animation: messageIn 0.5s cubic-bezier(0.16, 1, 0.3, 1) forwards;
        }

        .date-badge {
            display: inline-block;
            padding: 5px 16px;
            background: rgba(255, 255, 255, 0.04);
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            border: 1px solid rgba(255, 255, 255, 0.06);
            border-radius: 20px;
            color: rgba(200, 220, 210, 0.4);
            font-size: 11px;
            font-weight: 500;
        }

        .typing-indicator {
            display: flex;
            gap: 4px;
            padding: 14px 18px;
            align-items: center;
        }

        .typing-dot {
            width: 7px;
            height: 7px;
            background: rgba(0, 200, 150, 0.5);
            border-radius: 50%;
            animation: typingBounce 1.4s ease-in-out infinite;
        }

        .typing-dot:nth-child(2) { animation-delay: 0.2s; }
        .typing-dot:nth-child(3) { animation-delay: 0.4s; }

        @keyframes typingBounce {
            0%, 60%, 100% { transform: translateY(0); opacity: 0.4; }
            30% { transform: translateY(-8px); opacity: 1; }
        }

        .chat-input {
            padding: 16px 20px;
            background: rgba(255, 255, 255, 0.02);
            border-top: 1px solid rgba(255, 255, 255, 0.06);
            display: flex;
            align-items: center;
            gap: 12px;
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
        }

        .input-actions {
            display: flex;
            gap: 6px;
        }

        .input-btn {
            width: 38px;
            height: 38px;
            border-radius: 12px;
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.07);
            color: rgba(200, 220, 210, 0.5);
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
            flex-shrink: 0;
        }

        .input-btn:hover {
            background: rgba(0, 200, 150, 0.1);
            border-color: rgba(0, 200, 150, 0.2);
            color: #00c896;
        }

        .input-field {
            flex: 1;
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.07);
            border-radius: 16px;
            padding: 10px 16px;
            color: #d4ece4;
            font-size: 14px;
            font-family: 'Inter', sans-serif;
            outline: none;
            transition: all 0.3s ease;
        }

        .input-field::placeholder {
            color: rgba(200, 220, 210, 0.3);
        }

        .input-field:focus {
            border-color: rgba(0, 200, 150, 0.25);
            background: rgba(255, 255, 255, 0.07);
            box-shadow: 0 0 0 3px rgba(0, 200, 150, 0.08);
        }

        .send-btn {
            width: 42px;
            height: 42px;
            border-radius: 14px;
            background: linear-gradient(135deg, #00b482, #0088c8);
            border: none;
            color: white;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s ease;
            flex-shrink: 0;
            box-shadow: 0 4px 15px rgba(0, 180, 130, 0.25);
        }

        .send-btn:hover {
            transform: scale(1.05);
            box-shadow: 0 4px 25px rgba(0, 180, 130, 0.4);
        }

        .send-btn:active {
            transform: scale(0.95);
        }

        .read-receipt {
            display: inline-flex;
            align-items: center;
            margin-left: 4px;
        }

        .reaction-bar {
            display: flex;
            gap: 6px;
            margin-top: 6px;
        }

        .reaction {
            padding: 2px 8px;
            background: rgba(255, 255, 255, 0.06);
            border: 1px solid rgba(255, 255, 255, 0.06);
            border-radius: 12px;
            font-size: 13px;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .reaction:hover {
            background: rgba(255, 255, 255, 0.12);
            transform: scale(1.1);
        }

        .reaction.active {
            border-color: rgba(0, 200, 150, 0.3);
            background: rgba(0, 200, 150, 0.1);
        }

        @media (max-width: 520px) {
            body {
                padding: 0;
            }
            .chat-container {
                max-width: 100%;
                height: 100vh;
                max-height: 100vh;
                border-radius: 0;
            }
        }

        .glass-shimmer {
            position: absolute;
            top: 0;
            left: -100%;
            width: 50%;
            height: 100%;
            background: linear-gradient(90deg, transparent, rgba(255,255,255,0.03), transparent);
            animation: shimmer 8s ease-in-out infinite;
            pointer-events: none;
        }

        @keyframes shimmer {
            0% { left: -100%; }
            50% { left: 200%; }
            100% { left: 200%; }
        }

        .liquid-bg {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 0;
            overflow: hidden;
        }

        .liquid-shape {
            position: absolute;
            border-radius: 50%;
            background: linear-gradient(135deg, rgba(0, 200, 150, 0.06), rgba(0, 140, 200, 0.04));
            animation: liquidMove 15s ease-in-out infinite;
        }

        .liquid-shape:nth-child(1) {
            width: 200px;
            height: 200px;
            top: 10%;
            right: -60px;
            animation-delay: 0s;
        }

        .liquid-shape:nth-child(2) {
            width: 150px;
            height: 150px;
            bottom: 20%;
            left: -40px;
            animation-delay: -5s;
        }

        @keyframes liquidMove {
            0%, 100% { transform: translate(0, 0) rotate(0deg) scale(1); }
            25% { transform: translate(20px, -30px) rotate(45deg) scale(1.1); }
            50% { transform: translate(-15px, 20px) rotate(90deg) scale(0.9); }
            75% { transform: translate(10px, 10px) rotate(135deg) scale(1.05); }
        }
    </style>
</head>
<body>
    <div class="bg-orbs">
        <div class="bg-orb"></div>
        <div class="bg-orb"></div>
        <div class="bg-orb"></div>
    </div>

    <div class="chat-container">
        <div class="glass-shimmer"></div>
        <div class="liquid-bg">
            <div class="liquid-shape"></div>
            <div class="liquid-shape"></div>
        </div>

        <div class="chat-header">
            <img src="https://image.qwenlm.ai/public_source/75191d68-30fe-4dff-b914-dc280a23394b/1dccbdd94-3550-4a83-b304-d58afd6ad06d.png" alt="Аватар" class="header-avatar">
            <div class="header-info">
                <div class="header-title">Команда разработки</div>
                <div class="header-subtitle"><span class="online-dot"></span> 3 участника онлайн</div>
            </div>
            <div class="header-actions">
                <button class="header-btn" title="Поиск">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8"/><path d="m21 21-4.3-4.3"/></svg>
                </button>
                <button class="header-btn" title="Меню">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="1"/><circle cx="19" cy="12" r="1"/><circle cx="5" cy="12" r="1"/></svg>
                </button>
            </div>
        </div>

        <div class="chat-messages" id="chatMessages">
        </div>

        <div class="chat-input">
            <div class="input-actions">
                <button class="input-btn" title="Прикрепить" onclick="attachFile()">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m21.44 11.05-9.19 9.19a6 6 0 0 1-8.49-8.49l9.19-9.19a4 4 0 0 1 5.66 5.66l-9.2 9.19a2 2 0 0 1-2.83-2.83l8.49-8.48"/></svg>
                </button>
                <button class="input-btn" title="Голосовое" onclick="sendVoice()">
                    <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 2a3 3 0 0 0-3 3v7a3 3 0 0 0 6 0V5a3 3 0 0 0-3-3Z"/><path d="M19 10v2a7 7 0 0 1-14 0v-2"/><line x1="12" x2="12" y1="19" y2="22"/></svg>
                </button>
            </div>
            <input type="text" class="input-field" id="messageInput" placeholder="Сообщение..." onkeypress="handleKeyPress(event)">
            <button class="send-btn" onclick="sendMessage()" title="Отправить">
                <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M5 12h14"/><path d="m12 5 7 7-7 7"/></svg>
            </button>
        </div>
    </div>

    <script>
        const avatars = {
            alina: 'https://image.qwenlm.ai/public_source/75191d68-30fe-4dff-b914-dc280a23394b/1dccbdd94-3550-4a83-b304-d58afd6ad06d.png',
            dmitry: 'https://image.qwenlm.ai/public_source/75191d68-30fe-4dff-b914-dc280a23394b/151f285da-9955-498e-9f11-8d3bae52d824.png',
            maria: 'https://image.qwenlm.ai/public_source/75191d68-30fe-4dff-b914-dc280a23394b/14c14a7d1-2a7a-41dc-84f6-51409b592831.png',
            me: 'https://image.qwenlm.ai/public_source/75191d68-30fe-4dff-b914-dc280a23394b/1dccbdd94-3550-4a83-b304-d58afd6ad06d.png'
        };

        const chatData = [
            { type: 'date', text: 'Сегодня' },
            {
                type: 'received',
                sender: 'Алина',
                avatar: avatars.alina,
                text: 'Привет всем! 🎉 Как продвигается работа над новым дизайном?',
                time: '10:32',
                reactions: ['👋', '❤️']
            },
            {
                type: 'received',
                sender: 'Дмитрий',
                avatar: avatars.dmitry,
                text: 'Привет! Я закончил интеграцию API. Осталось протестировать несколько эндпоинтов.',
                time: '10:35'
            },
            {
                type: 'sent',
                text: 'Отлично, Дима! Скинь результат тестов, когда будет готово 👍',
                time: '10:36'
            },
            {
                type: 'received',
                sender: 'Мария',
                avatar: avatars.maria,
                isVideo: true,
                videoThumb: 'https://image.qwenlm.ai/public_source/75191d68-30fe-4dff-b914-dc280a23394b/19cf7bcb3-1bb2-48c0-bc99-aa808bd8a5e6.png',
                duration: '2:34',
                caption: 'Посмотрите демо нового интерфейса',
                time: '10:40',
                reactions: ['', '👀']
            },
            {
                type: 'received',
                sender: 'Алина',
                avatar: avatars.alina,
                text: 'Вау, Мария! Это выглядит потрясающе 😍 Жидкое стекло — это именно то, что мы хотели!',
                time: '10:42'
            },
            {
                type: 'sent',
                text: 'Полностью согласен! Эффект стекла на сообщениях — просто огонь 🔥',
                time: '10:43'
            },
            {
                type: 'received',
                sender: 'Дмитрий',
                avatar: avatars.dmitry,
                text: 'Да, анимации плавные. Но нужно проверить производительность на слабых устройствах.',
                time: '10:45'
            },
            {
                type: 'received',
                sender: 'Мария',
                avatar: avatars.maria,
                text: 'Уже оптимизировала! Убрала лишние backdrop-filter там, где не критично. Скорость выросла на 40% ',
                time: '10:48'
            },
            {
                type: 'sent',
                text: 'Супер! Давайте запланируем ревью кода на завтра в 15:00?',
                time: '10:50'
            },
            {
                type: 'received',
                sender: 'Алина',
                avatar: avatars.alina,
                text: 'Да, подходит! Я подготовлю список задач для следующего спринта ',
                time: '10:52'
            },
            {
                type: 'received',
                sender: 'Дмитрий',
                avatar: avatars.dmitry,
                isVideo: true,
                videoThumb: 'https://image.qwenlm.ai/public_source/75191d68-30fe-4dff-b914-dc280a23394b/151f285da-9955-498e-9f11-8d3bae52d824.png',
                duration: '1:12',
                caption: 'Результаты тестирования API',
                time: '10:55'
            },
            {
                type: 'sent',
                text: 'Всё чисто? ✅',
                time: '10:56'
            },
            {
                type: 'received',
                sender: 'Дмитрий',
                avatar: avatars.dmitry,
                text: 'Да, все 127 тестов прошли успешно! ',
                time: '10:57',
                reactions: ['', '💪']
            },
        ];

        const chatMessages = document.getElementById('chatMessages');
        let messageIndex = 0;

        function createMessageHTML(msg) {
            if (msg.type === 'date') {
                return `
                    <div class="date-separator">
                        <span class="date-badge">${msg.text}</span>
                    </div>
                `;
            }

            const isSent = msg.type === 'sent';
            const avatarSrc = isSent ? avatars.me : msg.avatar;
            const senderName = isSent ? '' : msg.sender;

            let bubbleContent = '';

            if (senderName) {
                bubbleContent += `<div class="sender-name">${senderName}</div>`;
            }

            if (msg.isVideo) {
                bubbleContent += `
                    <div class="video-message" onclick="playVideo(this)">
                        <img src="${msg.videoThumb}" class="video-thumb" alt="Video thumbnail">
                        <div class="video-overlay">
                            <div class="play-btn">
                                <svg width="20" height="20" viewBox="0 0 24 24" fill="white"><polygon points="5,3 19,12 5,21"/></svg>
                            </div>
                        </div>
                        <span class="video-duration">${msg.duration}</span>
                        <div class="video-info">
                            <div class="video-sender">${senderName}</div>
                            <div class="video-caption">${msg.caption}</div>
                        </div>
                    </div>
                `;
            } else {
                bubbleContent += `<div class="message-text">${msg.text}</div>`;
            }

            if (msg.reactions) {
                bubbleContent += `<div class="reaction-bar">`;
                msg.reactions.forEach(r => {
                    bubbleContent += `<span class="reaction">${r}</span>`;
                });
                bubbleContent += `</div>`;
            }

            let receipt = '';
            if (isSent) {
                receipt = `<span class="read-receipt"><svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="rgba(0,200,150,0.6)" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M2 12l5 5L17 7"/><path d="M7 12l5 5L22 7"/></svg></span>`;
            }

            bubbleContent += `<div class="message-time">${msg.time} ${receipt}</div>`;

            return `
                <div class="message-row ${msg.type}">
                    <img src="${avatarSrc}" alt="Avatar" class="avatar">
                    <div class="message-bubble">
                        ${bubbleContent}
                    </div>
                </div>
            `;
        }

        function loadMessages() {
            chatMessages.innerHTML = '';
            chatData.forEach((msg, idx) => {
                chatMessages.innerHTML += createMessageHTML(msg);
            });
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        function sendMessage() {
            const input = document.getElementById('messageInput');
            const text = input.value.trim();
            if (!text) return;

            const now = new Date();
            const time = now.getHours().toString().padStart(2, '0') + ':' + now.getMinutes().toString().padStart(2, '0');

            const msgHTML = createMessageHTML({
                type: 'sent',
                text: text,
                time: time
            });

            chatMessages.insertAdjacentHTML('beforeend', msgHTML);
            input.value = '';
            chatMessages.scrollTop = chatMessages.scrollHeight;

            setTimeout(() => {
                showTypingIndicator();
            }, 800);

            setTimeout(() => {
                removeTypingIndicator();
                const responses = [
                    { sender: 'Алина', avatar: avatars.alina, text: 'Поняла, спасибо! 💚' },
                    { sender: 'Дмитрий', avatar: avatars.dmitry, text: 'Хорошо, сделаю! 👍' },
                    { sender: 'Мария', avatar: avatars.maria, text: 'Интересно, расскажи подробнее ' },
                    { sender: 'Алина', avatar: avatars.alina, text: 'Отлично, продолжим завтра! ' },
                    { sender: 'Дмитрий', avatar: avatars.dmitry, text: 'Ок, записал ✅' },
                ];
                const resp = responses[Math.floor(Math.random() * responses.length)];
                const t = new Date();
                const rt = t.getHours().toString().padStart(2, '0') + ':' + t.getMinutes().toString().padStart(2, '0');

                const respHTML = createMessageHTML({
                    type: 'received',
                    sender: resp.sender,
                    avatar: resp.avatar,
                    text: resp.text,
                    time: rt
                });
                chatMessages.insertAdjacentHTML('beforeend', respHTML);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }, 2000);
        }

        let typingEl = null;

        function showTypingIndicator() {
            removeTypingIndicator();
            typingEl = document.createElement('div');
            typingEl.className = 'message-row received';
            typingEl.style.animationDelay = '0s';
            typingEl.innerHTML = `
                <img src="${avatars.alina}" alt="Avatar" class="avatar">
                <div class="message-bubble">
                    <div class="typing-indicator">
                        <div class="typing-dot"></div>
                        <div class="typing-dot"></div>
                        <div class="typing-dot"></div>
                    </div>
                </div>
            `;
            chatMessages.appendChild(typingEl);
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        function removeTypingIndicator() {
            if (typingEl && typingEl.parentNode) {
                typingEl.parentNode.removeChild(typingEl);
                typingEl = null;
            }
        }

        function handleKeyPress(e) {
            if (e.key === 'Enter') {
                sendMessage();
            }
        }

        function playVideo(el) {
            const overlay = el.querySelector('.video-overlay');
            const playBtn = el.querySelector('.play-btn');
            if (overlay.style.background === 'rgba(0, 0, 0, 0.5)') {
                overlay.style.background = '';
                playBtn.innerHTML = '<svg width="20" height="20" viewBox="0 0 24 24" fill="white"><polygon points="5,3 19,12 5,21"/></svg>';
            } else {
                overlay.style.background = 'rgba(0, 0, 0, 0.5)';
                playBtn.innerHTML = '<svg width="20" height="20" viewBox="0 0 24 24" fill="white"><rect x="6" y="4" width="4" height="16"/><rect x="14" y="4" width="4" height="16"/></svg>';
            }
        }

        function attachFile() {
            const responses = [
                { sender: 'Алина', avatar: avatars.alina, text: 'Файл прикреплён 📎' },
            ];
            const now = new Date();
            const time = now.getHours().toString().padStart(2, '0') + ':' + now.getMinutes().toString().padStart(2, '0');
            const msgHTML = createMessageHTML({
                type: 'sent',
                text: '📎 Файл прикреплён',
                time: time
            });
            chatMessages.insertAdjacentHTML('beforeend', msgHTML);
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        function sendVoice() {
            const now = new Date();
            const time = now.getHours().toString().padStart(2, '0') + ':' + now.getMinutes().toString().padStart(2, '0');
            const msgHTML = createMessageHTML({
                type: 'sent',
                text: '🎤 Голосовое сообщение (0:15)',
                time: time
            });
            chatMessages.insertAdjacentHTML('beforeend', msgHTML);
            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        // Toggle reactions on click
        document.addEventListener('click', function(e) {
            if (e.target.classList.contains('reaction')) {
                e.target.classList.toggle('active');
            }
        });

        // Initial load with animation
        window.addEventListener('load', () => {
            const rows = chatMessages.querySelectorAll('.message-row, .date-separator');
            rows.forEach((row, idx) => {
                row.style.animationDelay = `${idx * 0.1}s`;
            });
        });

        loadMessages();
    </script>
</body>
</html>

