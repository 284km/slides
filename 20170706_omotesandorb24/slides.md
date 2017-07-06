
## rspec コマンドの実行から
## 結果レポートまで
### 秒速@284km
##### 表参道.rb #24 ~テスト~

![goku.jpg](../goku.jpg)

---

# 秒速@284km
![goku.jpg](../goku.jpg)

- feedforce エンジニア
  - Ruby/Rails 中心の生活
  - サービスを作る人
- Rails Developers Meetup 運営

---

# 今日話すこと

- ## rspec コマンドの実行から、テスト結果のレポートまでのコードを説明します

---

# version: 3.7.0.pre

```
module RSpec
  module Core
    # Version information for RSpec Core.
    module Version
      # Current version of RSpec Core, in semantic versioning format.
      STRING = '3.7.0.pre'
    end
  end
end
```

---

## エントリーポイント
## rspec コマンド

rspec/rspec-core

```.rb
# exe/rspec

require 'rspec/core'
RSpec::Core::Runner.invoke
```

---

## RSpec::Core::Runner.invoke

```.rb
module RSpec
  module Core
    class Runner
      def self.invoke
        # 昔、require 'rspec/autorun' して実行されていたけれど
        # 今は deprecated なので、ここで disable にしている
        disable_autorun!
        # テストを実行してる(status: 0 if all specs passed)
        status = run(ARGV, $stderr, $stdout).to_i
        # status code を返してる
        exit(status) if status != 0
      end

      # @private
      def self.disable_autorun!
        @autorun_disabled = true
      end
```

---

## Class: RSpec::Core::Runner.run

Run a suite of RSpec examples.

- trap_interrupt

```.rb
module RSpec
  module Core
    class Runner
      def self.run(args, err=$stderr, out=$stdout)
        # SIGINT を受けると終了する
        trap_interrupt
        # コマンドライン引数をいろいろ parse している。説明するには長すぎる...。
        options = ConfigurationOptions.new(args)
        # parse した結果、runner が指定されていればそれを .call、無ければ new.run
        if options.options[:runner]
          options.options[:runner].call(options, err, out)
        else
          new(options).run(err, out)
        end
      end

      # @private
      def self.trap_interrupt
        trap('INT') { handle_interrupt }
      end

      # @private
      def self.handle_interrupt
        if RSpec.world.wants_to_quit
          exit!(1)
        else
          RSpec.world.wants_to_quit = true
          $stderr.puts "\nRSpec is shutting down and will print the summary report... Interrupt again to force quit."
        end
      end

# Internal container for global non-configuration data.
# グローバルで必要となる情報を持っておくヤツ
module RSpec
  module Core
    class World
      attr_accessor :wants_to_quit

```

---

## Class: RSpec::Core::Runner.run

Run a suite of RSpec examples.

- ConfigurationOptions.new(args)

```.rb
module RSpec
  module Core
    class Runner
      def self.run(args, err=$stderr, out=$stdout)
        # SIGINT を受けると終了する
        trap_interrupt
        # コマンドライン引数をいろいろ parse している。説明するには長すぎる...。
        options = ConfigurationOptions.new(args)
        # parse した結果、runner が指定されていればそれを .call、無ければ new.run
        if options.options[:runner]
          options.options[:runner].call(options, err, out)
        else
          new(options).run(err, out)
        end
      end

# ここは長すぎて説明はしない。こういう雰囲気で parse します
module RSpec
  module Core
    class ConfigurationOptions
      def initialize(args)
        @args = args.dup
        organize_options
      end

    private

      def organize_options
        @filter_manager_options = []

        @options = (file_options << command_line_options << env_options).each do |opts|
          @filter_manager_options << [:include, opts.delete(:inclusion_filter)] if opts.key?(:inclusion_filter)
          @filter_manager_options << [:exclude, opts.delete(:exclusion_filter)] if opts.key?(:exclusion_filter)
        end

        @options = @options.inject(:libs => [], :requires => []) do |hash, opts|
          hash.merge(opts) do |key, oldval, newval|
            [:libs, :requires].include?(key) ? oldval + newval : newval
          end
        end
      end

      def command_line_options
        @command_line_options ||= Parser.parse(@args)
      end

      def env_options
        return {} unless ENV['SPEC_OPTS']

        parse_args_ignoring_files_or_dirs_to_run(
          Shellwords.split(ENV["SPEC_OPTS"]),
          "ENV['SPEC_OPTS']"
        )
      end
```

---

## RSpec::Core::Runner#run

```
module RSpec
  module Core
    class Runner
      def run(err, out)
        setup(err, out)
        # example_groups を実行している example_group は、describe とか context の単位です
        run_specs(@world.ordered_example_groups).tap do
          persist_example_statuses
        end
      end

      def setup(err, out)
        @configuration.error_stream = err
        @configuration.output_stream = out if @configuration.output_stream == $stdout
        @options.configure(@configuration)
        # ここで spec file を読み込む
        @configuration.load_spec_files
        @world.announce_filters
      end

      # spec file を読み込んでいる
      def load_spec_files
        world.registered_example_group_files.each do |f|
          loaded_spec_files << f # the registered files are already expended absolute paths
        end

        files_to_run.uniq.each do |f|
          file = File.expand_path(f)
          # ここでファイルを load する
          load_spec_file_handling_errors(file)
          loaded_spec_files << file
        end

        @spec_files_loaded = true
      end

    private

      def load_spec_file_handling_errors(file)
        # ここで spec ファイルがただ load されている。つまり、実行される。
        load file
      rescue Support::AllExceptionsExceptOnesWeMustNotRescue => ex
        relative_file = Metadata.relative_path(file)
        reporter.notify_non_example_exception(ex, "An error occurred while loading #{relative_file}.")
        RSpec.world.wants_to_quit = true
      end
```

---

## RSpec::Core::Runner#run

```
module RSpec
  module Core
    class Runner
      def run(err, out)
        setup(err, out)
        # example_groups を実行している example_group は、describe とか context の単位です
        run_specs(@world.ordered_example_groups).tap do
          persist_example_statuses
        end
      end

      def run_specs(example_groups)
        examples_count = @world.example_count(example_groups)
        success = @configuration.reporter.report(examples_count) do |reporter|
          @configuration.with_suite_hooks do
            if examples_count == 0 && @configuration.fail_if_no_examples
              return @configuration.failure_exit_code
            end

            example_groups.map { |g| g.run(reporter) }.all?
          end
        end && !@world.non_example_failure

        success ? 0 : @configuration.failure_exit_code
      end

    private
      def persist_example_statuses
        return unless (path = @configuration.example_status_persistence_file_path)
        ExampleStatusPersister.persist(@world.all_examples, path)
      rescue SystemCallError => e
        RSpec.warning "Could not write example statuses to #{path} (configured as " \
                      "`config.example_status_persistence_file_path`) due to a " \
                      "system error: #{e.inspect}. Please check that the config " \
                      "option is set to an accessible, valid file path", :call_site => nil
      end


module RSpec
  module Core
    class ExampleStatusPersister
      def self.persist(examples, file_name)
        new(examples, file_name).persist
      end

      def initialize(examples, file_name)
        @examples  = examples
        @file_name = file_name
      end

      def persist
        RSpec::Support::DirectoryMaker.mkdir_p(File.dirname(@file_name))
        File.open(@file_name, File::RDWR | File::CREAT) do |f|
          f.flock(File::LOCK_EX)
          unparsed_previous_runs = f.read
          f.rewind
          f.write(dump_statuses(unparsed_previous_runs))
          f.flush
          f.truncate(f.pos)
        end
      end

```

---

