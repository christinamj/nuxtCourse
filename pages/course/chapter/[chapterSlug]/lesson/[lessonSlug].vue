<template>
  <div>
    <div class="prose mb-12">
      <p>Lesson {{ chapter.number }} - {{ lesson.number }}</p>
      <h2>{{ lesson.title }}</h2>
      <div class="flex space-x-4 mt-2 mb-8">
        <NuxtLink
          v-if="lesson.sourceUrl"
          class="font-normal text-md text-gray-500"
          :to="lesson.sourceUrl"
        >
          Download Source Code
        </NuxtLink>
        <a
          v-if="lesson.downloadUrl"
          class="font-normal text-md text-gray-500"
          :href="lesson.downloadUrl"
        >
          Download Video
        </a>
      </div>
      <VideoPlayer
        v-if="lesson.videoId"
        :videoId="lesson.videoId"
        :href="lesson.downloadUrl"
      />
      <p>{{ lesson.text }}</p>
      <LessonCompleteButton
        :modelValue="isLessonComplete"
        @update:modelValue="toggleComplete"
      ></LessonCompleteButton>
    </div>
  </div>
</template>

<script setup>
const course = useCourse();
const route = useRoute();
console.log(course);

const chapter = computed(() => {
  return course.chapters.find(
    (chapter) => chapter.slug === route.params.chapterSlug
  );
});

const lesson = computed(() => {
  return chapter.value.lessons.find(
    (lesson) => lesson.slug === route.params.lessonSlug
  );
});

const title = computed(() => {
  return `${lesson.value.title} - ${course.title}`;
});

useHead({
  title,
});

const progress = useLocalStorage("progress", []);

const isLessonComplete = computed(() => {
  if (!progress.value[chapter.value.number - 1]) {
    return false;
  }
  if (!progress.value[chapter.value.number - 1][lesson.value.number - 1]) {
    return false;
  }

  return progress.value[chapter.value.number - 1][lesson.value.number - 1];
});

const toggleComplete = () => {
  if (!progress.value[chapter.value.number - 1]) {
    progress.value[chapter.value.number - 1] = [];
  }

  progress.value[chapter.value.number - 1][lesson.value.number - 1] =
    !isLessonComplete.value;
};
</script>
