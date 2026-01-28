// 全局变量
let cardsContainer, adminBtn, adminPanel, closeAdmin, addPrizeBtn, prizesList, prizeModal, prizeForm, closeModal;
let addUserBtn, usersList, userModal, userForm, closeUserModal, effectsContainer;
let tabBtns, tabContents, backgroundItems, customBackgroundUrl, applyBackgroundUrl;
let systemNameInput, saveSystemSettings, systemTitle, footerTitle;

// 初始化数据
let users = [
    { id: 1, name: '张三', title: '产品经理', image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=professional%20businessman%20headshot&image_size=square_hd' },
    { id: 2, name: '李四', title: '前端开发', image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=professional%20frontend%20developer%20headshot&image_size=square_hd' },
    { id: 3, name: '王五', title: '后端开发', image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=professional%20backend%20developer%20headshot&image_size=square_hd' },
    { id: 4, name: '赵六', title: 'UI设计师', image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=professional%20UI%20designer%20headshot&image_size=square_hd' },
    { id: 5, name: '孙七', title: '测试工程师', image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=professional%20QA%20engineer%20headshot&image_size=square_hd' },
    { id: 6, name: '周八', title: '运维工程师', image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=professional%20DevOps%20engineer%20headshot&image_size=square_hd' }
];

let prizes = [
    { id: 1, name: 'iPhone 15', count: 1, image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=iPhone%2015%20product%20image&image_size=square_hd' },
    { id: 2, name: 'AirPods Pro', count: 2, image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=AirPods%20Pro%20product%20image&image_size=square_hd' },
    { id: 3, name: 'Apple Watch', count: 3, image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=Apple%20Watch%20product%20image&image_size=square_hd' },
    { id: 4, name: '星巴克咖啡券', count: 10, image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=Starbucks%20gift%20card&image_size=square_hd' }
];

// 存储已分配的奖品
let assignedPrizes = JSON.parse(localStorage.getItem('assignedPrizes')) || [];

// 初始化应用
function init() {
    // 初始化DOM元素
    cardsContainer = document.getElementById('cards-container');
    adminBtn = document.getElementById('adminBtn');
    adminPanel = document.getElementById('admin-panel');
    closeAdmin = document.getElementById('close-admin');
    addPrizeBtn = document.getElementById('add-prize');
    prizesList = document.getElementById('prizes-list');
    prizeModal = document.getElementById('prize-modal');
    prizeForm = document.getElementById('prize-form');
    closeModal = document.getElementById('close-modal');
    addUserBtn = document.getElementById('add-user');
    usersList = document.getElementById('users-list');
    userModal = document.getElementById('user-modal');
    userForm = document.getElementById('user-form');
    closeUserModal = document.getElementById('close-user-modal');
    effectsContainer = document.getElementById('effects-container');
    tabBtns = document.querySelectorAll('.tab-btn');
    tabContents = document.querySelectorAll('.tab-content');
    backgroundItems = document.querySelectorAll('.background-item');
    customBackgroundUrl = document.getElementById('custom-background-url');
    applyBackgroundUrl = document.getElementById('apply-background-url');
    systemNameInput = document.getElementById('system-name');
    saveSystemSettings = document.getElementById('save-system-settings');
    systemTitle = document.getElementById('system-title');
    footerTitle = document.getElementById('footer-title');
    
    // 从本地存储加载数据
    loadData();
    // 加载系统设置
    loadSystemSettings();
    // 加载背景
    loadBackground();
    // 生成卡片
    generateCards();
    // 生成奖品列表
    generatePrizesList();
    // 生成成员列表
    generateUsersList();
    // 绑定事件
    bindEvents();
}

// 从本地存储加载数据
function loadData() {
    const savedUsers = localStorage.getItem('users');
    const savedPrizes = localStorage.getItem('prizes');
    
    if (savedUsers) {
        users = JSON.parse(savedUsers);
    }
    
    if (savedPrizes) {
        prizes = JSON.parse(savedPrizes);
    }
}

// 保存数据到本地存储
function saveData() {
    localStorage.setItem('users', JSON.stringify(users));
    localStorage.setItem('prizes', JSON.stringify(prizes));
    localStorage.setItem('assignedPrizes', JSON.stringify(assignedPrizes));
}

// 加载系统设置
function loadSystemSettings() {
    const savedSystemName = localStorage.getItem('systemName');
    if (savedSystemName) {
        systemNameInput.value = savedSystemName;
        systemTitle.textContent = savedSystemName;
        footerTitle.textContent = savedSystemName;
        document.title = savedSystemName;
    }
}

// 保存系统设置
function saveSystemSettingsToStorage() {
    const systemName = systemNameInput.value.trim();
    if (systemName) {
        localStorage.setItem('systemName', systemName);
        systemTitle.textContent = systemName;
        footerTitle.textContent = systemName;
        document.title = systemName;
        alert('系统设置已保存');
    }
}

// 加载背景
function loadBackground() {
    const savedBackground = localStorage.getItem('background');
    if (savedBackground) {
        document.body.style.background = savedBackground;
    }
}

// 保存背景
function saveBackground(background) {
    localStorage.setItem('background', background);
    document.body.style.background = background;
}

// 检查用户是否已经参与过抽奖
function hasUserParticipated() {
    return localStorage.getItem('hasParticipated') === 'true';
}

// 标记用户已经参与过抽奖
function markUserAsParticipated() {
    localStorage.setItem('hasParticipated', 'true');
}

// 生成卡片
function generateCards() {
    cardsContainer.innerHTML = '';
    
    users.forEach((user, index) => {
        const card = document.createElement('div');
        card.className = 'card';
        card.style.animationDelay = `${index * 0.1}s`;
        
        card.innerHTML = `
            <div class="card-inner">
                <div class="card-front">
                    <img src="${user.image}" alt="${user.name}" class="card-image">
                    <h3 class="card-name">${user.name}</h3>
                    <p class="card-title">${user.title}</p>
                </div>
                <div class="card-back">
                    <h3 class="prize-name">点击翻转</h3>
                    <p class="prize-description">翻开卡片查看奖品</p>
                </div>
            </div>
        `;
        
        card.addEventListener('click', () => {
            // 检查用户是否已经参与过抽奖
            if (hasUserParticipated()) {
                alert('您已经参与过抽奖，每个用户只能参与一次！');
                return;
            }
            flipCard(card, user);
            // 标记用户已经参与过抽奖
            markUserAsParticipated();
        });
        cardsContainer.appendChild(card);
    });
}

// 卡片翻转逻辑
function flipCard(card, user) {
    // 检查是否已经翻转过
    if (card.classList.contains('flipped')) {
        return;
    }
    
    // 标记为已翻转
    card.classList.add('flipped');
    
    // 显示翻卡片特效（只保留彩带粒子效果）
    showFlipEffects(card);
    
    // 获取用户的奖品
    let prize = getAssignedPrize(user.id);
    
    // 如果没有分配奖品，随机分配一个
    if (!prize) {
        prize = assignPrize(user.id);
    }
    
    // 更新卡片背面内容
    const cardBack = card.querySelector('.card-back');
    cardBack.innerHTML = `
        <h3 class="prize-name">${prize.name}</h3>
        <img src="${prize.image}" alt="${prize.name}" class="prize-image">
        <p class="prize-description">恭喜您获得此奖品！</p>
    `;
}

// 显示翻卡片特效（只保留彩带粒子效果）
function showFlipEffects(card) {
    // 获取卡片位置
    const rect = card.getBoundingClientRect();
    const centerX = rect.left + rect.width / 2;
    const centerY = rect.top + rect.height / 2;
    
    // 只创建彩带特效
    createConfetti(centerX, centerY);
}

// 创建彩带特效
function createConfetti(x, y) {
    const colors = ['#ff6b6b', '#4ecdc4', '#45b7d1', '#96ceb4', '#ffeaa7', '#dfe6e9', '#e17055', '#00b894', '#0984e3', '#a29bfe'];
    
    for (let i = 0; i < 50; i++) {
        const confetti = document.createElement('div');
        confetti.className = 'confetti';
        confetti.style.setProperty('--color', colors[Math.floor(Math.random() * colors.length)]);
        confetti.style.left = `${x + (Math.random() - 0.5) * 300}px`;
        confetti.style.top = `${y + (Math.random() - 0.5) * 300}px`;
        confetti.style.animationDelay = `${i * 0.05}s`;
        confetti.style.animationDuration = `${3 + Math.random() * 2}s`;
        
        effectsContainer.appendChild(confetti);
        
        // 5秒后移除
        setTimeout(() => {
            confetti.remove();
        }, 5000);
    }
}

// 获取已分配的奖品
function getAssignedPrize(userId) {
    const assignment = assignedPrizes.find(a => a.userId === userId);
    if (assignment) {
        return prizes.find(p => p.id === assignment.prizeId);
    }
    return null;
}

// 分配奖品
function assignPrize(userId) {
    // 过滤出还有剩余数量的奖品
    const availablePrizes = prizes.filter(prize => {
        const assignedCount = assignedPrizes.filter(a => a.prizeId === prize.id).length;
        return assignedCount < prize.count;
    });
    
    // 如果没有可用奖品，返回默认奖品
    if (availablePrizes.length === 0) {
        return { name: '感谢参与', image: 'https://trae-api-cn.mchost.guru/api/ide/v1/text_to_image?prompt=thank%20you%20participation%20badge&image_size=square_hd' };
    }
    
    // 随机选择一个奖品
    const randomIndex = Math.floor(Math.random() * availablePrizes.length);
    const selectedPrize = availablePrizes[randomIndex];
    
    // 记录分配
    assignedPrizes.push({ userId, prizeId: selectedPrize.id });
    
    // 保存数据
    saveData();
    
    return selectedPrize;
}

// 生成奖品列表
function generatePrizesList() {
    prizesList.innerHTML = '';
    
    prizes.forEach(prize => {
        const prizeItem = document.createElement('div');
        prizeItem.className = 'prize-item';
        
        prizeItem.innerHTML = `
            <div class="prize-info">
                <h4>${prize.name}</h4>
                <p>数量: ${prize.count}</p>
            </div>
            <div class="prize-actions">
                <button class="edit-btn" data-id="${prize.id}">编辑</button>
                <button class="delete-btn" data-id="${prize.id}">删除</button>
            </div>
        `;
        
        prizesList.appendChild(prizeItem);
    });
    
    // 绑定编辑和删除事件
    document.querySelectorAll('.edit-btn').forEach(btn => {
        btn.addEventListener('click', (e) => editPrize(e.target.dataset.id));
    });
    
    document.querySelectorAll('.delete-btn').forEach(btn => {
        btn.addEventListener('click', (e) => deletePrize(e.target.dataset.id));
    });
}

// 生成成员列表
function generateUsersList() {
    usersList.innerHTML = '';
    
    users.forEach(user => {
        const userItem = document.createElement('div');
        userItem.className = 'user-item';
        
        userItem.innerHTML = `
            <div class="user-info">
                <h4>${user.name}</h4>
                <p>职位: ${user.title}</p>
            </div>
            <div class="user-actions">
                <button class="edit-user-btn" data-id="${user.id}">编辑</button>
                <button class="delete-user-btn" data-id="${user.id}">删除</button>
            </div>
        `;
        
        usersList.appendChild(userItem);
    });
    
    // 绑定编辑和删除事件
    document.querySelectorAll('.edit-user-btn').forEach(btn => {
        btn.addEventListener('click', (e) => editUser(e.target.dataset.id));
    });
    
    document.querySelectorAll('.delete-user-btn').forEach(btn => {
        btn.addEventListener('click', (e) => deleteUser(e.target.dataset.id));
    });
}

// 编辑奖品
function editPrize(id) {
    const prize = prizes.find(p => p.id === parseInt(id));
    if (prize) {
        document.getElementById('prize-id').value = prize.id;
        document.getElementById('prize-name').value = prize.name;
        document.getElementById('prize-image').value = prize.image;
        document.getElementById('prize-count').value = prize.count;
        prizeModal.classList.add('show');
    }
}

// 删除奖品
function deletePrize(id) {
    if (confirm('确定要删除这个奖品吗？')) {
        prizes = prizes.filter(p => p.id !== parseInt(id));
        // 同时删除相关的分配记录
        assignedPrizes = assignedPrizes.filter(a => a.prizeId !== parseInt(id));
        saveData();
        generatePrizesList();
    }
}

// 编辑成员
function editUser(id) {
    const user = users.find(u => u.id === parseInt(id));
    if (user) {
        document.getElementById('user-id').value = user.id;
        document.getElementById('user-name').value = user.name;
        document.getElementById('user-title').value = user.title;
        document.getElementById('user-image').value = user.image;
        userModal.classList.add('show');
    }
}

// 删除成员
function deleteUser(id) {
    if (confirm('确定要删除这个成员吗？')) {
        users = users.filter(u => u.id !== parseInt(id));
        // 同时删除相关的分配记录
        assignedPrizes = assignedPrizes.filter(a => a.userId !== parseInt(id));
        saveData();
        generateUsersList();
        generateCards();
    }
}

// 应用自定义背景URL
function applyCustomBackgroundUrl() {
    const url = customBackgroundUrl.value.trim();
    if (url) {
        const background = `url('${url}') no-repeat center center fixed`;
        document.body.style.background = background;
        document.body.style.backgroundSize = 'cover';
        localStorage.setItem('background', background);
        alert('背景已应用');
    }
}

// 绑定事件
function bindEvents() {
    // 管理员入口
    adminBtn.addEventListener('click', () => {
        adminPanel.classList.add('open');
    });
    
    // 关闭管理员后台
    closeAdmin.addEventListener('click', () => {
        adminPanel.classList.remove('open');
    });
    
    // 添加奖品
    addPrizeBtn.addEventListener('click', () => {
        document.getElementById('prize-id').value = '';
        document.getElementById('prize-name').value = '';
        document.getElementById('prize-image').value = '';
        document.getElementById('prize-count').value = 1;
        prizeModal.classList.add('show');
    });
    
    // 关闭模态框
    closeModal.addEventListener('click', () => {
        prizeModal.classList.remove('show');
    });
    
    // 提交奖品表单
    prizeForm.addEventListener('submit', (e) => {
        e.preventDefault();
        
        const id = document.getElementById('prize-id').value;
        const name = document.getElementById('prize-name').value;
        const image = document.getElementById('prize-image').value;
        const count = parseInt(document.getElementById('prize-count').value);
        
        if (id) {
            // 更新现有奖品
            const prizeIndex = prizes.findIndex(p => p.id === parseInt(id));
            if (prizeIndex !== -1) {
                prizes[prizeIndex] = { id: parseInt(id), name, image, count };
            }
        } else {
            // 添加新奖品
            const newId = Math.max(...prizes.map(p => p.id), 0) + 1;
            prizes.push({ id: newId, name, image, count });
        }
        
        // 保存数据
        saveData();
        // 更新奖品列表
        generatePrizesList();
        // 关闭模态框
        prizeModal.classList.remove('show');
    });
    
    // 添加成员
    addUserBtn.addEventListener('click', () => {
        document.getElementById('user-id').value = '';
        document.getElementById('user-name').value = '';
        document.getElementById('user-title').value = '';
        document.getElementById('user-image').value = '';
        userModal.classList.add('show');
    });
    
    // 关闭成员模态框
    closeUserModal.addEventListener('click', () => {
        userModal.classList.remove('show');
    });
    
    // 提交成员表单
    userForm.addEventListener('submit', (e) => {
        e.preventDefault();
        
        const id = document.getElementById('user-id').value;
        const name = document.getElementById('user-name').value;
        const title = document.getElementById('user-title').value;
        const image = document.getElementById('user-image').value;
        
        if (id) {
            // 更新现有成员
            const userIndex = users.findIndex(u => u.id === parseInt(id));
            if (userIndex !== -1) {
                users[userIndex] = { id: parseInt(id), name, title, image };
            }
        } else {
            // 添加新成员
            const newId = Math.max(...users.map(u => u.id), 0) + 1;
            users.push({ id: newId, name, title, image });
        }
        
        // 保存数据
        saveData();
        // 更新成员列表
        generateUsersList();
        // 更新卡片
        generateCards();
        // 关闭模态框
        userModal.classList.remove('show');
    });
    
    // 标签页切换
    tabBtns.forEach(btn => {
        btn.addEventListener('click', () => {
            const tab = btn.dataset.tab;
            
            // 更新标签按钮状态
            tabBtns.forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
            
            // 更新标签内容状态
            tabContents.forEach(content => {
                content.classList.remove('active');
                if (content.id === `${tab}-tab`) {
                    content.classList.add('active');
                }
            });
        });
    });
    
    // 背景选择
    backgroundItems.forEach(item => {
        item.addEventListener('click', () => {
            const background = item.dataset.bg;
            saveBackground(background);
        });
    });
    
    // 应用自定义背景URL
    applyBackgroundUrl.addEventListener('click', applyCustomBackgroundUrl);
    
    // 保存系统设置
    saveSystemSettings.addEventListener('click', saveSystemSettingsToStorage);
    
    // 点击模态框外部关闭
    window.addEventListener('click', (e) => {
        if (e.target === prizeModal) {
            prizeModal.classList.remove('show');
        }
        if (e.target === userModal) {
            userModal.classList.remove('show');
        }
    });
}

// 初始化应用
init();
