<script lang="ts" setup>
import { reactive, ref } from 'vue';

// リアクティブな変数を定義する
// const title = ref('タイトル');
// const description = ref('説明');

const taskForm = reactive({
  title: '',
  description: '',
  priority: '',
  expiresAt: '',
  tags: '',
});

const status = ref<'idle' | 'pending' | 'success' | 'error'>('idle');

async function addTask() {
  status.value = 'pending';

  const requestUrl = 'http://localhost:8787/api/v1/tasks';
  const response = await fetch(requestUrl, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      title: taskForm.title,
      description: taskForm.description,
      priority: taskForm.priority || undefined,
      expiresAt: taskForm.expiresAt || undefined,
      tags: taskForm.tags
        .split(',')
        .map(tag => tag.trim())
        .filter(Boolean),
    }),
  });

  // HTTP ステータスコードが 200 以外の場合
  if (!response.ok) {
    status.value = 'error';
    return;
  }

  // レスポンスのボディを JSON オブジェクトにパース
  const data = await response.json();
  console.log(data);

  status.value = 'success';
}

function clearForm() {
  taskForm.title = '';
  taskForm.description = '';
  taskForm.priority = '';
  taskForm.expiresAt = '';
  taskForm.tags = '';
}
</script>

<template>
  <section :class="$style.container">
    <h1 :class="$style.heading">タスクを追加する</h1>

    <form :class="$style.form" novalidate>
      <div :class="$style.field">
        <label for="title" :class="$style.label">タスク名</label>
        <input
          id="title"
          :class="$style.input"
          type="text"
          inputmode="text"
          placeholder="タスク名"
          v-model="taskForm.title"
        />
      </div>

      <div :class="$style.field">
        <label for="description" :class="$style.label">説明</label>
        <textarea
          id="description"
          :class="$style.textarea"
          rows="4"
          placeholder="説明を入力"
          v-model="taskForm.description"
        ></textarea>
        <p :class="$style.help">任意。詳細やメモを記載</p>
      </div>

      <div :class="$style.fieldInline">
        <div :class="$style.field">
          <label for="priority" :class="$style.label">優先度</label>
          <select id="priority" :class="$style.select" v-model="taskForm.priority">
            <option value="">-</option>
            <option value="low">低</option>
            <option value="medium">中</option>
            <option value="high">高</option>
            <option value="urgent">緊急</option>
          </select>
        </div>

        <div :class="$style.field">
          <label for="due" :class="$style.label">期限</label>
          <input id="due" :class="$style.input" type="date" placeholder="YYYY-MM-DD" v-model="taskForm.expiresAt" />
        </div>
      </div>

      <div :class="$style.field">
        <label for="tags" :class="$style.label">タグ</label>
        <input id="tags" :class="$style.input" type="text" placeholder="例: work, urgent" v-model="taskForm.tags" />
        <p :class="$style.help">カンマ区切りで入力</p>
      </div>

      <div :class="$style.actions">
        <button
          :class="[$style.button, $style.primary]"
          type="button"
          :disabled="status === 'pending'"
          @click="addTask"
        >
          追加
        </button>
        <button :class="[$style.button, $style.ghost]" type="button" @click="clearForm">クリア</button>
      </div>

      <Transition name="alert">
        <div
          v-if="status === 'success'"
          :class="[$style.alert, $style.alertSuccess]"
          role="status"
          aria-live="polite"
          aria-atomic="true"
        >
          <svg
            :class="$style.alertIcon"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 24 24"
            width="20"
            height="20"
            aria-hidden="true"
          >
            <path
              fill="currentColor"
              d="M12 2a10 10 0 1 0 0 20 10 10 0 0 0 0-20Zm-1.05 13.29-3.2-3.2a1 1 0 0 1 1.41-1.41l2.49 2.49 4.49-4.49a1 1 0 1 1 1.41 1.41l-5.2 5.2a1 1 0 0 1-1.4 0Z"
            />
          </svg>
          <p :class="$style.alertText">タスクを作成しました</p>
        </div>
      </Transition>

      <Transition name="alert">
        <div
          v-if="status === 'error'"
          :class="[$style.alert, $style.alertError]"
          role="alert"
          aria-live="assertive"
          aria-atomic="true"
        >
          <svg
            :class="$style.alertIcon"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 24 24"
            width="20"
            height="20"
            aria-hidden="true"
          >
            <path fill="currentColor" d="M12 2a10 10 0 1 0 0 20 10 10 0 0 0 0-20Zm1 14h-2v-2h2v2Zm0-4h-2V6h2v6Z" />
          </svg>
          <p :class="$style.alertText">タスクの作成に失敗しました</p>
        </div>
      </Transition>
    </form>
  </section>
</template>

<style lang="scss" module>
.container {
  max-width: 720px;
  margin: 0 auto;
  padding: 1.5rem 1rem;
}

.heading {
  margin: 0 0 1rem 0;
  font-size: 1.5rem;
  font-weight: 600;
  color: var(--color-text);
  text-align: left;
}

.form {
  background-color: var(--color-background);
  border: 1px solid var(--color-border);
  border-radius: 0.75rem;
  padding: 1rem;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.fieldInline {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
  margin-bottom: 1rem;
}

.label {
  font-size: 0.875rem;
  color: var(--color-text);
}

.input,
.textarea,
.select {
  width: 100%;
  background-color: transparent;
  color: var(--color-text);
  border: 1px solid var(--color-border);
  border-radius: 0.5rem;
  padding: 0.625rem 0.75rem;
}

.textarea {
  resize: vertical;
}

.input:focus,
.textarea:focus,
.select:focus {
  outline: 2px solid hsla(160, 100%, 37%, 0.35);
  border-color: hsla(160, 100%, 37%, 1);
}

.help {
  font-size: 0.75rem;
  color: var(--color-text);
  opacity: 0.7;
  margin: 0;
}

.error {
  font-size: 0.75rem;
  color: #c85151;
  margin: 0;
}

.actions {
  display: flex;
  gap: 0.75rem;
  margin-top: 0.5rem;
}

.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0.6rem 1rem;
  border-radius: 0.5rem;
  border: 0;
  cursor: pointer;
  font-weight: 600;
}

.primary {
  background-color: hsla(160, 100%, 37%, 1);
  color: var(--color-background);
}

.primary:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.ghost {
  background-color: transparent;
  color: var(--color-text);
  border: 1px solid var(--color-border);
}

/* Alert */
.alert {
  display: flex;
  align-items: flex-start;
  gap: 0.5rem;
  margin-top: 1rem;
  padding: 0.75rem 0.875rem;
  border-radius: 0.5rem;
  border: 1px solid transparent;
}

.alertIcon {
  flex: 0 0 auto;
  margin-top: 1px;
}

.alertText {
  margin: 0;
  font-size: 0.875rem;
}

.alertSuccess {
  background: hsla(160, 84%, 39%, 0.12);
  color: hsla(160, 84%, 34%, 1);
  border-color: hsla(160, 84%, 39%, 0.35);
}

.alertError {
  background: rgba(200, 81, 81, 0.12);
  color: #c85151;
  border-color: rgba(200, 81, 81, 0.35);
}

/* Transition */
.alert-enter-active,
.alert-leave-active {
  transition: all 180ms ease;
}

.alert-enter-from,
.alert-leave-to {
  opacity: 0;
  transform: translateY(-4px);
}
</style>
