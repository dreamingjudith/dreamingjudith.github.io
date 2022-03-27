---
layout: post
title: "PyTorch-Lightning 1.5에서 args로 하이퍼파라미터 전달"
categories: python
tags: pytorch-lightning deeplearning
---

재작년(...) 사내 양성과정의 일환으로 진행했던 미니 프로젝트를 다시 끄집어냈다. 팀 프로젝트로 진행했던 작업이긴 하나 소스코드를 너무 대충 짜논 턱에 못 알아보겠는 부분도 많고, 너무 오래 전에 작업했던 내용이라 그 사이에 SKT에서 KoGPT2 version 2를 내고 카카오에서도 KoGPT 발표하고 난리가 나서 주말마다 리팩토링을 해보기로 했다.

2020년 때와의 가장 큰 차이점이라고 한다면 역시 PyTorch-Lightning이 정식 버전을 내고 .5가 붙었다는 건데, 이 때문에 안 맞는 API가 너~무 많았다. (지금 리팩토링하면서 사용하고 있는 PyTorch-Lightning은 버전 1.5.10) 그 중에서도 특히나 골때리는 게 2개 정도 있었는데 이번에는 argparse를 이용해 파싱한 args를 LightningModule 내에서 하이퍼파라미터로 사용할 수 있게 전달하는 부분을 다룰 생각이다.

2년 전에는 `__init__`에서 하이퍼파라미터 저장을 이렇게 했다.

```python
class CMPersonaChat(LightningModule):
    def __init__(self, hparams, *args):
        super(CMPersonaChat, self).__init__()
        self.hparams = hparams  # <-- 이 방식으로 지정
        self.kogpt2 = get_kogpt2_model(hparams.model_params)
```

물론 지금은 안 된다. `self.hparams`에 직접 값을 지정하는 것이 불가능해졌기 때문에 `save_hyperparameters()`를 이용해야 한다.

```python
class CMPersonaChat(LightningModule):
    def __init__(self, hparams, *args):
        super(CMPersonaChat, self).__init__()
        self.save_hyperparameters()  # <-- 여기가 바로 달라진 부분
        self.kogpt2 = get_kogpt2_model()
```

근데 이래놨는데 `configure_optimizers`에서 lr이 제대로 읽히지 않는 것이었다.

```python
/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/trainer/trainer.py:1584: UserWarning: GPU available but not used. Set the gpus flag in your trainer `Trainer(gpus=1)` or script `--gpus=1`.
  rank_zero_warn(
Traceback (most recent call last):
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/utilities/parsing.py", line 272, in __getattr__
    return self[key]
KeyError: 'lr'

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "main.py", line 493, in <module>
    main()
  File "main.py", line 365, in main
    train(dataloader, args)
  File "main.py", line 280, in train
    trainer.fit(model, dataloader['train'], dataloader['valid'])
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/trainer/trainer.py", line 740, in fit
    self._call_and_handle_interrupt(
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/trainer/trainer.py", line 685, in _call_and_handle_interrupt
    return trainer_fn(*args, **kwargs)
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/trainer/trainer.py", line 777, in _fit_impl
    self._run(model, ckpt_path=ckpt_path)
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/trainer/trainer.py", line 1145, in _run
    self.accelerator.setup(self)
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/accelerators/cpu.py", line 35, in setup
    return super().setup(trainer)
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/accelerators/accelerator.py", line 93, in setup
    self.setup_optimizers(trainer)
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/accelerators/accelerator.py", line 354, in setup_optimizers
    optimizers, lr_schedulers, optimizer_frequencies = self.training_type_plugin.init_optimizers(
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/plugins/training_type/training_type_plugin.py", line 245, in init_optimizers
    return trainer.init_optimizers(model)
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/trainer/optimizers.py", line 35, in init_optimizers
    optim_conf = self.call_hook("configure_optimizers", pl_module=pl_module)
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/trainer/trainer.py", line 1501, in call_hook
    output = model_fx(*args, **kwargs)
  File "main.py", line 166, in configure_optimizers
    lr=self.hparams.lr, correct_bias=False)
  File "/home/judith/miniconda3/envs/cm/lib/python3.8/site-packages/pytorch_lightning/utilities/parsing.py", line 274, in __getattr__
    raise AttributeError(f'Missing attribute "{key}"') from exp
AttributeError: Missing attribute "lr"
```

2년 전까지만 해도 잘 돌던 코드였는데 갑자기 왜 이러나 싶어서 `__init__`에서 `self.hparams`를 출력해봤는데 좀 골때리게 나오고 있었다.

```python
"hparams": Namespace(accelerator=None, accumulate_grad_batches=None, amp_backend='native', amp_level=None, auto_lr_find=False, auto_scale_batch_size=False, auto_select_gpus=False, benchmark=False, check_val_every_n_epoch=1, checkpoint_callback=None, dataset_cache='./dataset_cache', dataset_path='new_dataset/personachat_translated_sample.json', default_root_dir=None, detect_anomaly=False, deterministic=False, device='cuda', devices=None, enable_checkpointing=True, enable_model_summary=True, enable_progress_bar=True, eval_dataset_path='eval/eval_merge.json', fast_dev_run=False, flush_logs_every_n_steps=None, gpus=None, gradient_clip_algorithm=None, gradient_clip_val=None, ipus=None, limit_predict_batches=1.0, limit_test_batches=1.0, limit_train_batches=1.0, limit_val_batches=1.0, log_every_n_steps=50, log_gpu_memory=None, logger=True, lr=5e-05, max_epochs=None, max_history=2, max_len=768, max_steps=-1, max_time=None, min_epochs=None, min_length=1, min_steps=None, mode='train', model_params=None, move_metrics_to_cpu=False, multiple_trainloader_mode='max_size_cycle', name='cm_kogpt2', no_sample=False, num_candidates=1, num_eval_pp=10, num_nodes=1, num_processes=1, num_sanity_val_steps=2, num_workers=8, overfit_batches=0.0, personality_permutations=1, plugins=None, precision=32, prepare_data_per_node=None, process_position=0, profiler=None, progress_bar_refresh_rate=None, reload_dataloaders_every_epoch=False, reload_dataloaders_every_n_epochs=0, replace_sampler_ddp=True, resume_from_checkpoint=None, stochastic_weight_avg=False, strategy=None, sync_batchnorm=False, temperature=0.7, terminate_on_nan=None, top_k=0, top_p=0.9, tpu_cores=None, track_grad_norm=-1, train_batch_size=4, val_check_interval=1.0, valid_batch_size=2, warmup_ratio=0.1, weights_save_path=None, weights_summary='top')
```

`self.hparams`가 왜 `dict`로 받아지는 거야...? ㅋㅋㅋㅋㅋㅋㅋ... 이렇게 되면 `self.hparams['hparams'].lr` 와 같은 식으로 써야 한다는 건데, 말도 안 되는 짓이었다. 아마 2년 사이에 PyTorch-Lightning의 코딩 철학에 많은 변화가 생긴 모양이었다.

그래서 [문서](https://pytorch-lightning.readthedocs.io/en/1.5.10/common/hyperparameters.html)를 다시 뒤져 확인한 뒤 코드를 몇 군데 수정했다.

```python
# 모델 정의
class CMPersonaChat(LightningModule):
    def __init__(self, **hparams):  # should get hparams with ** if you want pass args
    # def __init__(self, hparams):  # not like this
        super(CMPersonaChat, self).__init__()
        self.save_hyperparameters()
        self.kogpt2 = get_kogpt2_model()

# 실제 모델 불러서 args 넘기는 부분
parser = CMPersonaChat.add_model_specific_args(parser)
parser = Trainer.add_argparse_args(parser)
args = parser.parse_args()

model = CMPersonaChat(**vars(args))  # <-- namespace인 args를 dict로 바꾸고 key-value pair로 던져야 한다
```

수정 결과, LightningModule 내에서 하이퍼파라미터가 필요할 때 `self.hparams.lr` 과 같은 식으로 불러도 코드가 제대로 실행되는 걸 확인했다. 그런데 웬만하면 PyTorch-Lightning 쪽에서 `dict`나 `namespace` 둘 다 받아지게 지원을 해줬으면 좋겠다. 딱히 어려운 건 아닌데!